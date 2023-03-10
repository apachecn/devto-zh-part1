# tensorflow 的 docker 赞赏贴和用例

> 原文：<https://dev.to/bgalvao/a-docker-appreciation-post-and-use-case-with-tensorflow-3dg4>

最近我不得不使用 tensorflow 的代码库，不是当前的 1.4 版本，而是 1.2.1 版本。

> 机器智能的开源软件库
> [tensorflow.org](//tensorflow.org)

因此，tensorflow 是一个允许您实现机器学习算法的库，即神经网络架构。它允许您使用 CPU(更少的核心，更高的时钟速度)或使用 GPU(更多的核心，更低的时钟速度)来训练神经网络。因为神经网络在 GPU 中是高度并行的，所以我对使用 GPU 运行 tensorflow 很感兴趣:更少的训练时间，更多的参数调整迭代。

然而，tensorflow 对于一些 nvidia 库有一些特定的要求，以便在 GPU 上运行:

*   cuda 8.0
*   cudnn 6.0

我在工作中使用的一些传统软件和其他软件需要 cuda 9.0...我曾经在工作中在我的系统上安装和卸载过一些东西，我不想因为一个较小的项目任务而有如此多的麻烦。

所以我对自己说“如果有一个小环境，我可以在 GPU 上运行 tensorflow 就好了...就像码头工人的形象或什么的”。我的想法被听到了，当我发现 tensorflow 可以从 docker 容器和 GPU 上运行时，我简直不敢相信自己的眼睛:)

如果你不知道 docker 是什么或如何使用它，我建议你看下面的视频或同一个人 Jonny L 写的教程,我会尽力在这里总结一下。

[https://www.youtube.com/embed/JprTjTViaEA](https://www.youtube.com/embed/JprTjTViaEA)

Docker 是一个集装箱系统。它允许你使用精简版本的操作系统(比如 111mb 的 ubuntu ),并为一个特定的应用建立一个特定的镜像。这就像运行别人的计算机系统和配置，除了你在你自己的计算机中运行它，因为容器足够轻。

为了澄清，请允许我快速介绍一些概念:

*   一个**映像**就像一个操作系统的快照。它不是运行的东西。
*   一个**容器**是一个图像的实例。您从一个映像启动一个容器，您可以让它运行或停止它。容器有自己的文件系统，就像你的机器一样。
*   在本文中，**卷**是您指定在主机系统(即您的机器)和正在运行的容器之间共享的目录。

在我的例子中，我试图解决“不在我的机器上运行”的问题。^1 因此，tensorflow(该组织)在 [docker hub](https://hub.docker.com/r/tensorflow/tensorflow/) 上提供图片。对我来说，这是一个找到遗留图像标签`1.2.1-gpu-py3`的问题。所以首先，我做了

```
docker pull tensorflow/tensorflow:1.2.1-gpu-py3 
```

Enter fullscreen mode Exit fullscreen mode

所以现在，当您运行`docker images`时，您可以看到这个特定的图像被列出。为了从这个映像启动一个容器，我运行了

```
nvidia-docker run -it --rm -p 8888:8888 --name followme -v $(pwd):/notebooks tensorflow/tensorflow:1.2.1-gpu-py3 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细分析一下这个命令:

*   [`nvidia-docker`](https://github.com/NVIDIA/nvidia-docker) 只是这个特殊问题的特例，提供 GPU 对 docker 容器的访问，正如 tensorflow 所指示的。否则，大多数用例将简单地使用`docker`。
*   是启动容器的 docker 子命令
*   `-it`是启用交互式 tty 的标志，或者简单地说，是容器的终端。
*   `--rm`是指示在退出容器后自动移除容器的标志。
*   `-p 8888:8888`是指示将容器的端口 8888 连接到主机的端口 8888 的标志。我试图查找哪个对应哪个，但是我找不到这个，抱歉。端口 8888 是 jupyter 笔记本服务器的默认端口，包含在映像中。
*   将该容器命名为“followme”，鉴于该容器立即被移除，回头看是完全愚蠢的。你知道的越多越好。
*   `-v $(pwd):/notebooks`将容器的`/notebooks`目录连接到主机系统的当前工作目录`$(pwd)`。无论你在容器中做什么，都会被写到主机系统。
*   最后`tensorflow/tensorflow:1.2.1-gpu-py3`是这个实例开始的映像。
*   在这个特殊的例子中，这个命令默认启动 jupyter 笔记本服务器。但是如果您需要对容器“会话”有更多的控制，您可以在上面的命令后面添加`bash`来在终端上启动 bash 会话。

有了它，我就能够使用别人的遗留代码在 GPU 上更快地训练神经网络。我发现这是 docker 所能做的一个很好的例子。我迫不及待地开始构建特定的映像，用 docker 控制和分离我的开发环境，作为一名更专注于代码的开发人员，过上更快乐的生活:)

^1 另一个用例可能包括一个或类似的或全部的以下问题:使用旧 linux 内核的旧 os 版本；仅与旧内核版本兼容的相机驱动程序；定制内核补丁，将打破你心爱的机器。在这种情况下，最好在 docker 容器中测试所有这些安装乱七八糟的东西，并保持您的系统完好无损:)