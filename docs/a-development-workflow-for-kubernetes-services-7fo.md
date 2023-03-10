# Kubernetes 服务的开发工作流

> 原文：<https://dev.to/datawireio/a-development-workflow-for-kubernetes-services-7fo>

Kubernetes 服务的基本开发工作流允许开发人员编写一些代码，提交代码，然后在 Kubernetes 上运行。同样重要的是，您的开发环境要尽可能地类似于生产环境，因为拥有两个不同的环境将不可避免地引入 bug。在本教程中，我们将浏览一个围绕 Kubernetes、Docker 和 Envoy/Ambassador 构建的基本开发工作流。

## 您的云基础设施

本教程依赖于云中的两个组件，Kubernetes 和 Ambassador。如果您还没有，请继续设置它们。

*   [Kubernetes](https://kubernetes.io)
*   Kubernetes 的自助式 API 网关

## 1。Kubernetes 服务的开发环境

您需要一个 Kubernetes 服务的开发环境。我们建议采用以下方法:

*   一个容器化的*构建/运行时*环境，您的服务总是在其中运行和构建。将您的环境容器化有助于确保不同开发和生产环境之间的环境平等。它还简化了新开发人员的入职流程。
*   在集群之外本地开发您的微服务。你想要一个快速的编码/构建/测试周期。如果进行远程开发，部署到 Kubernetes 集群的额外步骤会带来很大的延迟。
*   一旦您需要与他人共享您的服务(例如，金丝雀测试、内部开发等),就将您的服务部署到 Kubernetes 中。).

您需要在笔记本电脑上安装以下工具:

*   git，用于源代码控制
*   Docker，来构建和运行您的容器
*   `kubectl`，管理您的部署
*   [Forge](https://forge.sh) ，用于将您的服务部署到 Kubernetes
*   [网真](https://www.telepresence.io)，用于本地开发您的服务

如果您还没有安装它们，现在就开始安装吧。

## 2。向 Kubernetes 部署服务

在传统应用程序中，发布/运营团队管理应用程序更新到生产的部署。在微服务架构中，团队负责将服务更新部署到生产环境中。

我们将从 source 向 Kubernetes 部署并发布一项微服务。

1.  我们创建了一个简单的 Python 微服务，您可以将它用作您的服务的模板。该模板包括:

*   一个`Dockerfile`，指定如何配置和构建您的开发环境和运行时环境。
*   一个为不同场景定制部署的`service.yaml`文件(例如，生产、金丝雀、开发)。
*   一个 Kubernetes 清单(`k8s/deployment.yaml`)，它定义了服务如何在 Kubernetes 中运行。它还包含为给定服务配置大使所需的注释。

```
 git clone https://github.com/datawire/hello-world-python 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们将使用 Forge 来自动化和模板化部署过程。运行 Forge 配置流程:

```
 forge setup 
```

Enter fullscreen mode Exit fullscreen mode

1.  让服务在 Kubernetes 集群上运行的过程包括许多步骤:构建 Docker 映像、将映像推送到存储库、实例化指向映像的 Kubernetes 清单，以及将清单应用到集群。Forge 自动化了整个部署过程:

```
 cd hello-world-python
   forge deploy 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，我们将测试服务。获取大使的外部 IP 地址:

```
 kubectl get services ambassador
   NAME         CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
   ambassador   10.11.250.208   35.190.189.139   80:31622/TCP   4d 
```

Enter fullscreen mode Exit fullscreen mode

1.  通过大使访问服务:

```
 curl 35.190.189.139/hello/
   Hello World (Python)! (up 0:03:13) 
```

Enter fullscreen mode Exit fullscreen mode

## 3。实时编码

开发时，您需要快速的反馈周期。您希望进行代码更改，并立即能够构建和测试您的代码。我们刚刚经历的部署流程增加了流程的延迟，因为构建和部署包含最新更改的容器需要时间。然而，在 Kubernetes 中运行服务允许该服务访问其他云资源(例如，其他服务、数据库等)。).

[Telepresence](https://www.telepresence.io) 允许您在本地开发服务，同时创建远程 Kubernetes 集群的双向代理。

1.  您希望您的开发环境与您的运行时环境相同。为此，我们将使用与生产中完全相同的 docker 文件来构建开发映像。确保您在`hello-world-python`目录中，并键入:

```
 docker build . -t hello-world-dev 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，我们可以将 Kubernetes 上现有的`hello-world`服务替换为相同服务的一个版本，运行在本地容器中。

```
 telepresence --swap-deployment hello-world-stable --docker-run \
    --rm -it -v $(pwd):/service hello-world-dev:latest 
```

Enter fullscreen mode Exit fullscreen mode

(请注意，Forge 已经自动在部署名称后面添加了一个`stable`后缀，以表明该服务已经使用在`service.yaml`中指定的`stable`概要文件进行了部署。)

1.  网真调用`docker run`来启动容器。它还将包含 Python 源代码树的本地文件系统挂载到容器中。将`app.py`中的“Hello World”消息更改为不同的值:

```
 def root(): 
    return "Hello World via Telepresence! (up %s)\n" % elapsed() 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，如果我们通过 Ambassador 测试我们的服务，我们会看到我们现在正路由到我们服务的修改版本。

```
 curl 35.190.189.139/hello/
   Hello World via Telepresence! (up 0:04:13) 
```

Enter fullscreen mode Exit fullscreen mode

## 想了解更多？

这篇文章最初出现在 [Datawire](https://www.datawire.io/) 的[代码快速指南](https://www.datawire.io/faster/)上。查看本系列中的其他教程:

*   [为什么您的开发工作流程对微服务如此重要](https://www.datawire.io/faster/why-workflow/)
*   [Canary 部署、A/B 测试和微服务](https://www.datawire.io/faster/canary-workflow/)
*   [共享开发模式和多服务应用](https://www.datawire.io/faster/shared-dev/)

或者尝试本教程中提到的开源项目:

*   [Kubernetes](https://kubernetes.io)
*   大使
*   [伪造](https://forge.sh)
*   [网真](https://www.telepresence.io/)
*   [码头工人](https://www.docker.com/)

如果您有任何问题，请通过 [Gitter](https://gitter.im/datawire/home) 联系我们。