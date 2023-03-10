# 使用容器实例在 Azure 中运行 Go

> 原文：<https://dev.to/tophatsteve/running-go-in-azure-with-container-instances>

在 Azure 中，Go 不像在谷歌云平台上那样是官方支持的语言。通过安装“用于 Azure 网站的 Go Lang”扩展，你可以*类似于*将 Go 代码作为 web 应用的一部分运行，但这实际上只允许你将代码作为 IIS 管道的一部分运行，而不是作为独立的服务器。在 Azure 中运行实际 Go 服务器的最佳方式是通过容器。幸运的是，微软刚刚发布了 [**容器实例**](https://azure.microsoft.com/en-us/services/container-instances/) ，它提供了一种简单而廉价的方式在云中构建 Docker 容器。

下面将带您完成我用来在容器实例中运行 Go web 服务器的步骤。

### 构建 web 服务器

首先，我创建了以下小型 Go 应用程序。它监听端口 80 上的连接，并以文本“Hello Azure”作为响应。可以通过将环境变量 port 设置为您想要的任何值来更改端口。

```
package main

import (
  "fmt"
  "log"
  "net/http"
  "os"
)

func main() {
  port := os.Getenv("PORT")

  if port == "" {
    port = "80"
  }

  http.HandleFunc("/", handler)
  err := http.ListenAndServe(":"+port, nil)
  if err != nil {
    log.Fatal("ListenAndServe: ", err)
  }
}

func handler(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Hello Azure")
} 
```

Enter fullscreen mode Exit fullscreen mode

### 构建容器图像

Go 代码需要构建为 linux 二进制文件才能在 Docker 容器中运行，所以我在构建之前将 GOOS 环境变量设置为“Linux”。因为我使用 scratch Docker 映像作为基本映像，所以我还需要通过将 CGO _ 启用设置为 0，并传入“-a”和“-installsuffix cgo”标志，将代码编译为纯静态二进制文件。

```
CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main . 
```

Enter fullscreen mode Exit fullscreen mode

我的 Docker 文件基于 scratch base 映像，它只是一个空映像。这允许我创建尽可能小的图像，比我编译的二进制文件大不了多少。该文件将服务器编译后的二进制文件复制到映像中，然后将其设置为在映像运行时启动。

```
FROM scratch
ADD main main
ENV PORT 80
EXPOSE 80
ENTRYPOINT ["/main"] 
```

Enter fullscreen mode Exit fullscreen mode

我构建了 Docker 映像，然后使用以下命令在本地运行它以进行测试。这在本地主机的端口 80 上运行它。

```
docker build -t tophatsteve/my-go-app .
docker run -d -p 80:80 tophatsteve/my-go-app 
```

Enter fullscreen mode Exit fullscreen mode

### 将容器部署到 Azure

Azure 从容器注册表中提取图像，所以我需要将我的图像发布到一个容器注册表中。我选择使用 Docker Hub，但是使用私有注册中心也同样容易。为了推送至 Docker Hub，我使用了以下命令(我需要首先使用“Docker Login”命令来登录我的 Docker Hub 帐户)。

```
docker push tophatsteve/my-go-app 
```

Enter fullscreen mode Exit fullscreen mode

容器实例目前似乎不能通过 Azure 门户配置，所以我需要使用 Azure CLI。如果你想从本地计算机运行这些命令，那么
你需要安装最新版本的 [Azure CLI](https://docs.microsoft.com/en-gb/cli/azure/install-azure-cli) 。或者，它们可以在浏览器的[云壳](https://azure.microsoft.com/en-gb/features/cloud-shell/)中运行。

我首先创建了一个资源组来保存我的容器实例。容器实例目前并非在所有地区都可用，所以我选择了西欧，这是离我最近的提供支持的地区。

```
az group create --name MyContainerInstances --location westeurope 
```

Enter fullscreen mode Exit fullscreen mode

它返回下面的 JSON。重要的部分是 *provisioningState* 设置，设置为 succeeded。

```
{  "id":  "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyContainerInstances",  "location":  "westeurope",  "managedBy":  null,  "name":  "MyContainerInstances",  "properties":  {  "provisioningState":  "Succeeded"  },  "tags":  null  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我运行以下命令来提供我的容器实例。我告诉它我想要的实例名称，将它放在哪个资源组中，以及使用什么 Docker 映像。我还添加了 *- ip-address public* 标志，以允许公开访问实例。

```
az container create --name mycontainer --image tophatsteve/my-go-app \
--resource-group MyContainerInstances --ip-address public 
```

Enter fullscreen mode Exit fullscreen mode

返回了以下 JSON 输出。

```
{  "containers":  [  {  "command":  null,  "environmentVariables":  [],  "image":  "tophatsteve/my-go-app",  "instanceView":  null,  "name":  "mycontainer",  "ports":  [  {  "port":  80  }  ],  "resources":  {  "limits":  null,  "requests":  {  "cpu":  1.0,  "memoryInGb":  1.5  }  },  "volumeMounts":  null  }  ],  "id":  "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyContainerInstances/providers/Microsoft.ContainerInstance/containerGroups/mycontainer",  "imageRegistryCredentials":  null,  "ipAddress":  {  "ip":  "13.69.11.29",  "ports":  [  {  "port":  80,  "protocol":  "TCP"  }  ]  },  "location":  "westeurope",  "name":  "mycontainer",  "osType":  "Linux",  "provisioningState":  "Creating",  "resourceGroup":  "MyContainerInstances",  "restartPolicy":  null,  "state":  null,  "tags":  null,  "type":  "Microsoft.ContainerInstance/containerGroups",  "volumes":  null  } 
```

Enter fullscreen mode Exit fullscreen mode

从这个输出中需要注意的重要事情是容器正在运行的 *ipAddress* 、容器的 *provisioningState* 和 *state* 。在这种情况下，容器将在 IP 地址 13.69.11.29 上运行，配置状态为“正在创建”，状态为空。在 *provisioningState* 为“Succeeded”并且*状态*为“Running”之前，我们无法访问容器，因此我们需要等待容器启动。

几分钟后，我通过运行下面的命令检查容器的状态，这将产生大量的 JSON 输出。

```
az container show --name mycontainer --resource-group MyContainerInstances 
```

Enter fullscreen mode Exit fullscreen mode

输出的底部看起来像这样，其中 *provisioningState* 为“Succeeded”,*状态*为“Running ”,显示容器现在准备好了。

```
...  "location":  "westeurope",  "name":  "mycontainer",  "osType":  "Linux",  "provisioningState":  "Succeeded",  "resourceGroup":  "MyContainerInstances",  "restartPolicy":  null,  "state":  "Running",  "tags":  null,  "type":  "Microsoft.ContainerInstance/containerGroups",  "volumes":  null  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 测试容器

当实例成功运行时，为了测试它，我将浏览器指向创建实例时给我的 IP 地址。

[![Hello Azure](img/4f71ff99d30d7c5f9cd2e0a0ffc0a739.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bagnQ9hK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://camelcase.co.uk/img/hello-azure-browser.PNG)

### 删除容器

为了避免不必要的成本，最后一步是使用以下命令删除实例。

```
az container delete --name mycontainer --resource-group MyContainerInstances 
```

Enter fullscreen mode Exit fullscreen mode