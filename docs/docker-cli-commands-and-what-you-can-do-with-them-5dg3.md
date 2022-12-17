# Docker CLI 命令及其用途

> 原文：<https://dev.to/twaintaylor/docker-cli-commands-and-what-you-can-do-with-them-5dg3>

Docker 改变了应用程序的开发方式。自 2014 年推出以来的短短时间内，Docker 已经在几乎所有关心软件开发质量和效率的开发组织中找到了一席之地。这种增长主要是由开发商推动的。开发人员如此热衷 Docker 的一个原因是它简单而强大的命令列表。过去需要多次点击和长命令的事情现在变成了简单的命令，易于记忆和构建。

Docker 新手？让我们更深入地了解 Docker CLI 的伟大之处，以及一些最常用的命令。

Docker 支持分层的命令结构。下面的 docker 命令列表由基本命令 Docker 的子命令组成，如上所述。

本文涵盖了入门 Docker 所需的最基本的命令。参见这个 [Docker CLI](https://www.aquasec.com/wiki/display/containers/Docker+CLI+Commands) wiki 页面，获得一堆有用的资源，帮助你开始。

### docker 帮助

在开始使用 Docker 时，这是一个有用的命令。运行 docker help 命令会向您显示 docker 命令的语法，以及命令列表和对其功能的解释。

要获得任何特定命令的详细信息，只需附加命令名，如下所示:

`docker help run`

这个命令将向您显示 run 命令的详细内容。

### docker 运行

run 命令在指定的图像上创建一个可写的容器层。以下是该命令的语法:

`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`

docker run 命令必须指定从中派生容器的容器映像。您可以使用“运行”命令的选项添加或覆盖图像默认值。您可以将容器作为前台或后台进程运行，设置重启策略，配置网络设置，以及强制限制容器可以消耗的资源。

### 码头工人拉

docker pull 命令从 Docker Hub 这样的存储库中下载一个映像。您可以选择像 Quay.io 这样的私有注册中心，但是 pull 命令保持不变。

`docker pull [OPTIONS] image_name:tag`

默认情况下，所有注册表扫描任何下载的图像，以检查常见的已知漏洞。使用 docker pull 你可以跳过这个安全扫描，虽然，你可能永远不需要，因为它是一个好习惯，扫描所有下载的图像。

pull 命令提取单个图像，但是您可以使用-a 标记将其设置为提取多个图像。此外，当 pull 命令正在运行时，您仍然可以使用 ctrl+C 取消它。

### docker 提交

对于开发人员来说，尤其是那些实践 DevOps 的大型团队的一部分，在特性中“提交”小变化的过程对于构建伟大的软件是必不可少的。Docker 支持这种频繁、少量更新的模式，甚至为此提供了 docker commit 命令。

`docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`

一旦您对容器映像进行了更改，并且想要发布您的更改，您可以使用 docker commit 命令。

### RMI 坞站

下载完成后，如果您想删除某个图像，可以使用 rmi 命令。您可以使用以下语法删除一个或多个图像:

`docker rmi [OPTIONS] IMAGE [IMAGE...]`

默认情况下，rmi 会删除该映像和所有父映像，如果您想保留未标记的父映像，可以附加- no-prune 选项。

### docker rm

rm 命令删除容器，并使用以下格式:

`docker rm [OPTIONS] CONTAINER [CONTAINER...]`

如果容器已经停止，您可以按原样运行该命令。但是如果容器仍然作为守护进程运行，您需要附加-f 选项来强制删除容器。

### 码头检查

inspect 命令帮助您检查图像或容器，并查看有关它们的元数据。

`docker inspect [OPTIONS] NAME|ID [NAME|ID...]`

inspect 命令帮助您监控容器健康状况、环境变量等。它检索标签、操作系统版本、IP 地址、MAC 地址等重要信息。要获取容器的具体信息，可以使用-f 参数。结果以 JSON 格式显示。

### 码头工人停止&码头工人杀

使用容器意味着你必须不断地结束进程和终止容器。docker stop 命令停止在容器中运行的进程，并在终止之前给容器一个宽限期。

`docker stop [OPTIONS] CONTAINER [CONTAINER...]`

docker kill 命令会立即终止容器。

`docker kill [OPTIONS] CONTAINER [CONTAINER...]`

### docker 重启

当您手动停止或终止容器时，或者如果它们遭到破坏，或者如果它们在运行时失败，您会希望重新启动它们以保持应用程序的弹性。在这种情况下，docker restart 命令可以完成这项工作。

`docker restart [OPTIONS] CONTAINER [CONTAINER...]`

还有其他像 systemd 这样重启容器的方法，但这是 Docker 推荐的方法。重启容器的另一个有效方法是使用 Kubernetes 重启策略。

### docker ps

当您想在一个视图中查看所有的容器时，可以使用 docker ps 命令。它提供了所有容器的系统记录，包括在主机上运行和退出的容器。

`docker restart [OPTIONS] CONTAINER [CONTAINER...]`

您可以使用选项来显示最近创建的容器列表，或者查看它们的文件大小。这是一个用容器“盘点库存”的好方法，可以看到你在任何给定的时刻都在运行什么。

### 结论

这些是最常用的 Docker 命令，它们帮助您在日常工作中执行最基本的功能。为了充分利用这些命令，您应该熟悉每个命令的选项。然而，它们并不是全部。您可以在 Docker 文档中查看[完整列表](https://docs.docker.com/engine/reference/commandline/docker/)。

当您使用 Docker 容器时，它们使开发变得更加容易和强大。Docker 命令适用于从提取图像、检查图像到提交图像的所有操作。关键是掌握 Docker CLI 和您可以使用的各种命令。这包括每个命令独有的选项。Docker 的优势之一是其简单而强大的 CLI，我们已经在这篇文章中看到了证明。