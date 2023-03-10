# 使用 Docker 和 Kubernetes 快速开发工作流

> 原文：<https://dev.to/datawireio/fast-development-workflow-with-docker-and-kubernetes-1if>

保持开发环境的同步是一个持续的痛苦。容器化您的开发环境使您的服务可以在任何地方运行在完全相同的环境中:从您的笔记本电脑到生产环境。

[Telepresence](https://www.telepresence.io/) 与容器化的开发环境相结合，为开发人员在 Kubernetes 上开发多容器应用程序提供了快速的开发流程。Telepresence 允许您在本地运行 Docker 容器，同时将其代理到您的 Kubernetes 集群。

在本 HOWTO 中，我们将介绍如何在集装箱化的 Docker 环境中使用远程呈现来构建快速开发工作流。

## 安装网真

首先，你需要用 Homebrew/apt/dnf 安装网真。你可以在这里找到安装说明。

## 快速举例

我们从一个简单的例子开始。从运行集群中的服务开始:

```
$  kubectl run qotm --image=datawire/qotm:1.3 --port=5000 --expose
$  kubectl get service qotm
NAME        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE qotm        10.0.0.12    <none>  8000/TCP   1m 
```

Enter fullscreen mode Exit fullscreen mode

运行服务器的 pod 可能需要一两分钟才能启动并运行，这取决于您的群集的速度。

现在，您可以使用 Telepresence 运行 Docker 容器来访问该服务，即使流程是本地的，但服务是在 Kubernetes 集群中运行的:

```
$  telepresence --docker-run -i -t alpine /bin/sh
alpine#  apk add --no-cache curl
alpine#  curl http://qotm:5000/
{
  "hostname": "qotm-1536849512-ckf1v",
  "ok": true,
  "quote": "Nihilism gambles with lives, happiness, and even destiny itself!",
  "time": "2017-10-25T15:28:51.712799",
  "version": "1.3"
} 
```

Enter fullscreen mode Exit fullscreen mode

(如果 QOTM 吊舱还没有启动，这将不起作用...如果是，请再试一次。)

## 在 Docker 中设置开发环境

那么，我们如何使用网真来进行 QOTM 服务的实际开发呢？我们将为 QOTM 建立一个本地的 Dockerized 开发环境。克隆 QOTM 回购:

```
$ git clone https://github.com/datawire/qotm.git 
```

Enter fullscreen mode Exit fullscreen mode

存储库中有一个 [Dockerfile](https://github.com/datawire/qotm/blob/master/Dockerfile) ，它为 QOTM 服务构建了一个运行时环境。

构建运行时环境:

```
$ cd qotm
$ docker build -t qotm-dev . 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 Telepresence 将 QOTM 部署与本地 Docker 映像进行交换。在幕后，Telepresence 调用`docker run`，因此它支持您可以传递给`docker run`的任何参数。在这种情况下，我们还将把本地目录挂载到 Docker 容器中的`/service`。确保您当前的工作目录是`qotm`目录，因为我们将把它直接挂载到容器中。

```
$ telepresence --swap-deployment qotm --docker-run \
  --rm -it -v $(pwd):/service qotm-dev:latest 
```

Enter fullscreen mode Exit fullscreen mode

我们可以测试一下。在另一个终端中，我们将在 Kubernetes 集群上远程启动一个 pod。

```
$ kubectl run -i --tty alpine --image=alpine -- sh
/ # apk add --no-cache curl
...
/ # curl http://qotm:5000
{
  "hostname": "8b4faa7e175c",
  "ok": true,
  "quote": "The last sentence you read is often sensible nonsense.",
  "time": "2017-10-25T19:28:41.038335",
  "version": "1.3"
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来换一下`qotm.py`里的版本。运行以下命令:

```
sed -i -e 's@1.3@'"1.4"'@' qotm/qotm.py 
```

Enter fullscreen mode Exit fullscreen mode

从您的远程 pod:
重新运行`curl`命令

```
/ # curl http://qotm:5000
{
  "hostname": "8b4faa7e175c",
  "ok": true,
  "quote": "The last sentence you read is often sensible nonsense.",
  "time": "2017-10-25T19:28:41.038335",
  "version": "1.4"
} 
```

Enter fullscreen mode Exit fullscreen mode

注意代码是如何变化的。恭喜你！您现在已经:

*   将 QOTM 服务路由到本地运行的 Docker 容器
*   配置 Docker 服务以从本地文件系统中获取更改
*   进行实时代码编辑，并看到它立即反映在生产中

## 工作原理

Telepresence 将启动一个新的代理容器，然后使用您传递给`--docker-run`的任何参数调用`docker run`来启动一个将代理其网络的容器。所有网络都被代理:

*   去了库伯内特。
*   添加了`--also-proxy`的外向云资源
*   到用`--expose`指定的端口的输入连接。

来自远程`Deployment`的卷和环境变量也在容器中可用。

## 附加资源

如果你对自己尝试网真感兴趣，你可以[用家酿、apt 或 dnf 在本地](https://www.telepresence.io/reference/install)安装。

或者查看其他教程:

*   [调试 Kubernetes 上的服务](https://www.telepresence.io/tutorials/kubernetes)
*   [通过谷歌容器引擎使用网真](https://cloud.google.com/community/tutorials/developing-services-with-k8s)
*   【OpenShift 和网真入门
*   [网真和迷你库](https://www.telepresence.io/tutorials/minikube-vpn)

有问题吗？在网真 [Gitter 聊天室](https://gitter.im/datawire/telepresence)提问或者[在 GitHub](https://github.com/datawire/telepresence/issues/new) 上提出问题。

*这篇文章最初是作为[网真文档](https://www.telepresence.io/tutorials/docker)的一部分出现的。*