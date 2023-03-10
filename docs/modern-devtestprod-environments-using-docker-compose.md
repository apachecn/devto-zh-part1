# 使用 Docker Compose 的现代开发/测试/生产环境

> 原文：<https://dev.to/poison_dv/modern-devtestprod-environments-using-docker-compose>

有成千上万的帖子谈论 Docker 是什么以及如何开始使用它，但是没有多少帖子简单地解释它背后的动机。所以我决定写一本，在我看来。

我认为理解*为什么*比理解*如何*的技术细节重要得多。当你理解了*为什么*，你就有了学习*如何*的动力。

好吧，让我们先提醒自己过去工作的旧习惯。这是为了更好地了解这个问题。

我们过去常常手动创建开发/测试/生产环境。当你的团队和环境很小的时候，它可能会起作用。当团队成长时，您必须共享那些开发/测试环境，事情开始失去控制。这样的环境不是孤立的，随着时间的推移会变得不一致。

在许多情况下，这些环境与生产环境不同，在不同的机器上运行，或者具有不同的网络/安全条件。这些都会导致生产中的意外错误。

当团队和环境增长时，工程师无法跟上新组件的不断变化和添加。不可能在本地机器上手动安装另一个依赖项。这导致了创造性的解决方案，比如使用其他环境中的 API/缓存/数据库。💩

这就是 Docker 通过将您的环境服务基础设施转化为代码而大放异彩的地方。这在您的工程师开发、CI 测试和生产环境之间是可预测、可重复和一致的。

## 码头工人

先说 Docker，它是一切的基础。像对待任何抽象概念一样，理解这个概念以及如何使用它需要一些时间。但是相信我这是值得的。

Docker 使用 Linux 核心，不需要为每个容器(服务)创建虚拟机。因此，开发机器的最佳选择是 Linux。Docker 也可以在 Mac 上运行，但是需要更多的资源来创建 Docker 虚拟机空间。抱歉，我无法提供任何关于在 Windows 操作系统中使用 Docker 的反馈，因为我从未尝试过。(如果你有，请在评论中分享你的经历)

Docker 容器代表什么？容器是您环境中一个组件的单个实例。例如，组件可以是 web 应用程序、API、工作器、缓存、数据库等。

您在 service Docker 文件中定义的内容是使用哪个操作系统映像作为您的基线，如何使用您的服务依赖项来丰富它，从哪里复制代码以及如何运行服务。

## 复合坞站

当你想开发一个应用程序/服务时，Docker 是个不错的选择。使它真正强大的是 Docker Compose，它创建了一个 Docker 容器的组合，最终代表了您真正的应用程序开发/测试/生产环境。

Docker 组合描述了服务之间的关系。它们的容量、网络、链接、配置等..

它允许你在你的机器上运行服务的整个沙盒环境。完全隔离，你自己的环境。☺

我认为这就是你需要知道的所有东西，这样你就有动力去学习如何构建 Docker 容器和使用 Composition。

如果我可以简单地解释一下，作为下一步，我建议遵循 [Docker 文档](https://docs.docker.com/learn/)。

祝你好运！