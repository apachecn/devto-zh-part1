# 你好码头工人

> 原文：<https://dev.to/rusrushal13/hello-docker>

Docker 是时下相当流行的一种新时尚。我以前对此了解不多，但在我实习时，我参加了 Dieter Reuter(码头工人船长)和来自 bee42 solutions gmbh 的 Niclas Mietz 的研讨会。

和他们一起参加为期两天的研讨会非常有趣，我也了解了很多。那么让我们从 Docker 开始吧。在开始使用它之前，你应该在你的机器上安装 Docker。接受谷歌的帮助。

好了，现在你的机器有 Docker，所以现在你可能在想 Docker 到底是什么？

Docker 是一个用于构建、发布和运行应用程序的开放平台。使用 Docker，您可以像管理应用程序一样管理基础设施。这很容易，简单，也相当强大。

众所周知，我们总是从 Hello-world 开始

`docker container run hello-world`

在输出中，你可以看到我们正在做很多事情(Docker 正在做的事情)。但在此之前，我想说明一些关于 Docker 引擎。

### 停靠引擎

[![](img/0ba7ea30029dd4c25c816555cfdd5139.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SePkNboU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z41mmtmgm2cqrf5bspwg.png)

Docker 就像你的汽车一样，它有一个管理一切的引擎。它还包含一个名为 Docker Daemon(dockerd 命令)的服务器，为客户端管理一切(Docker CLI)。在这两者之间，API 处理从客户机到服务器的请求。服务器通过管理映像、容器、网络和卷来相应地响应客户端。

Docker 守护进程:Docker 守护进程监听 API 请求并管理 Docker 对象(映像、容器、网络和卷)。一个守护进程也可以与其他守护进程通信来管理 Docker 服务(swarm 模式)。

Docker 客户端:Docker 客户端是许多 Docker 用户与 Docker 交互的方式。当您使用 docker run 这样的命令时，客户端会将这些命令发送给 docker，docker 会执行这些命令。docker 命令使用 Docker API。Docker 客户端可以与多个守护进程通信。

Docker 注册表:Docker 注册表存储 Docker 图像。Docker Hub 和 Docker Cloud 是任何人都可以使用的公共注册表，Docker 默认配置为在 Docker Hub 上查找图像。当您使用 docker pull 或 docker run 命令时，将从您配置的注册表中提取所需的映像。当您使用 docker push 命令时，您的映像会被推送到您配置的注册表中。您可以通过获取新版本的映像并重新部署容器来升级应用程序。

图像:图像是 Docker 容器的快照。通常，一个图像基于另一个图像，并进行一些额外的定制。例如，您可以构建一个基于 ubuntu 映像的映像，但是安装 Nginx web 服务器和您的应用程序，以及运行您的应用程序所需的配置细节。与其他虚拟化技术相比，映像重量轻、体积小、速度快。

容器:容器是一个图像的可运行实例。您可以使用 CLI 创建、运行、停止、移动或删除容器。您可以将容器连接到一个或多个网络，为其附加存储，甚至基于其当前状态创建新的映像。默认情况下，容器与其他容器及其主机相对隔离。您可以控制容器的网络、存储或其他底层子系统与其他容器或主机的隔离程度。容器由其映像以及创建或运行时提供给它的任何配置选项来定义。当容器停止时，没有存储在持久存储中的对其状态的任何更改都会消失。

服务:它允许您跨多个 Docker 守护进程扩展容器，这些守护进程与多个管理器和工人一起工作。群的每个成员都是一个 Docker 守护进程，这些守护进程都使用 Docker API 进行通信。服务允许您定义所需的状态，例如在任何给定时间必须可用的服务副本的数量。默认情况下，该服务在所有工作节点之间进行负载平衡。对于消费者来说，Docker 服务看起来是一个单独的应用程序。

在一些理论之后，现在让我们看看输出，

`Hello from Docker!`

1.  docker 客户端连接到 Docker 守护程序。
2.  Docker 守护进程从 Docker Hub 中提取“hello-world”映像。
3.  Docker 守护进程从该映像创建了一个新的容器，它运行可执行文件，生成您当前正在阅读的输出。
4.  Docker 守护进程将输出流式传输到 Docker 客户机，客户机将输出发送到您的终端。

就是这样，非常简单和容易。享受它的轻松。