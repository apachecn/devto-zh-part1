# Kubernetes 网真服务的快速发展

> 原文：<https://dev.to/kelseyevans/rapid-development-of-kubernetes-services-with-telepresence-7cd>

假设您正在开发一个新的 Kubernetes 服务。典型的测试方法是修改代码，重新构建映像，将映像推送到 Docker 注册中心，然后重新部署 Kubernetes `Deployment`。这可能会很慢。

或者，你可以使用[网真](https://www.telepresence.io)。网真将远程`Deployment`代理到你机器上运行的进程。
这意味着您可以在本地开发，边开发边编辑代码，但是要在 Kubernetes 集群中测试您的服务。

假设您在下面的最小服务器上工作，`helloworld.py` :

```
#!/usr/bin/env python3 
from http.server import BaseHTTPRequestHandler, HTTPServer

class RequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/plain')
        self.end_headers()
        self.wfile.write(b"Hello, world!\n")
        return

httpd = HTTPServer(('', 8080), RequestHandler)
httpd.serve_forever() 
```

Enter fullscreen mode Exit fullscreen mode

您在 Kubernetes 集群中启动一个代理，它将请求从集群转发到您的本地进程，并且在生成的 shell 中启动 web 服务器:

```
localhost$ telepresence --new-deployment hello-world --expose 8080
localhost$ python3 helloworld.py 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个名为`hello-world`的新的`Deployment`和`Service`，它们将在端口 8080 上侦听，并将流量转发到您机器上端口 8080 上的进程。

如果您在 Kubernetes 集群中启动一个容器并连接到那个`Service`，就可以看到这一点。
在新的终点站运行:

```
localhost$  kubectl --restart=Never run -i -t --image=alpine console /bin/sh
kubernetes#  wget -O - -q http://hello-world:8080/
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

现在，切换回另一个终端，删除`helloworld.py`并编辑它，使它返回一个不同的字符串。
比如:

```
python3 helloworld.py
^C localhost$  sed s/Hello/Goodbye/g -i helloworld.py
localhost$  grep Goodbye helloworld.py
 self.wfile.write(b"Goodbye, world!\n") localhost$  python3 helloworld.py 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经用新代码重启了本地流程，我们可以从另一个终端向它发送另一个查询，在那里我们有一个运行在 Kubernetes pod 中的 shell】

```
kubernetes#  wget -O - -q http://hello-world:8080/
Goodbye, world! kubernetes#  exit 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以在本地编辑代码，更改会立即反映到 Kubernetes 集群中的客户端，而无需重新部署、创建 Docker 映像等等。

### 附加资源

如果你对自己尝试网真感兴趣，你可以[用家酿、apt 或 dnf 在本地](https://www.telepresence.io/reference/install)安装。

或者查看其他教程:

*   [调试 Kubernetes 上的服务](https://www.telepresence.io/tutorials/kubernetes)
*   [通过谷歌容器引擎使用网真](https://cloud.google.com/community/tutorials/developing-services-with-k8s)
*   【OpenShift 和网真入门
*   [网真和迷你库](https://www.telepresence.io/tutorials/minikube-vpn)

有问题吗？在网真 [Gitter 聊天室](https://gitter.im/datawire/telepresence)提问或者[在 GitHub](https://github.com/datawire/telepresence/issues/new) 上提出问题。

*这篇文章最初出现在[网真文档](https://www.telepresence.io/tutorials/kubernetes-rapid)中。*