# IntelliJ 平台的自动化 API 生成

> 原文：<https://dev.to/dploeger/an-automated-api-generation-for-the-intellij-platform-4k0d>

如果你没有注意到:我已经[发布了一个由](https://plugins.jetbrains.com/plugin/9958-shell-filter) [Jetbrains](https://www.jetbrains.com/) 开发的 IntelliJ 平台的插件。为 IntelliJ 平台开发有时有点困难，因为我习惯有一个 API 文档作为参考，但不知何故 Jetbrains 没有为该平台提供一个。

在 github 查看他们的[源代码，我发现他们的代码实际上在大部分地方都用](https://github.com/JetBrains/intellij-community) [Javadoc](https://docs.oracle.com/javase/9/javadoc/) 注释进行了很好的注释，所以生成一个 API 文档应该没有*那么*难。

至少我是这么认为的。

但是 Javadoc 是个混蛋。它伴随着 JDK 而来，所以就有了第一个问题:什么 JDK？甲骨文？OpenJDK？8?9?我开始在装有最新的甲骨文 JDK 8 的 Mac 电脑上写作，一切都运行得很快。为了每天晚上自动生成文档，我认为使用 [Travis](https://travis-ci.org) 是一个好主意(提示:确实如此！).然而，Travis 使用相同的 JDK，但是在 linux 代理上，不知何故产生了不同的结果。

为了解决不同平台的问题，我把它们都打包在一个 docker 容器中。

*但是对于使用 Docker for Mac 的相当大的一组文件来说，I/O 性能好吗？是啊，当然。*

Docker for Mac 正在为其 Linux-Docker 主机使用 [HyperKit](https://github.com/moby/hyperkit) ，在 I/O 性能方面非常糟糕(Docker 确实在全 Linux 环境下工作得最好)。然而，我用 [docker-sync](http://docker-sync.io) 包装了一些东西，但发现它确实是 macOS 的利基解决方案。于是，我结束了一个 macOS-docker 解决方案和一个 linux 解决方案的编写。

*但是一个最新的 Oracle JDK 作为 docker 容器呢？是啊，当然。*

自动化安装 Oracle JDK 是一件非常痛苦的事情，而且没有官方甚至最新的 Docker 映像可用。因此，我使用了漂亮的 [OpenJDK 图像](https://hub.docker.com/_/openjdk/)。我使用了其中的 javadoc，但是当缺少一个库时(另一个 Javadoc 只是跳过了缺少的库)，无法让它产生输出。所以，最后，我用-Xold 告诉 OpenJDK 9 的 javadoc 使用旧的 javadoc 实现

它现在工作了，我们有一个[每日生成的 API 文档](https://dploeger.github.io/intellij-api-doc/)(参见 github 上的[生成器代码)，尽管我不太确定这个系统有多稳定。所以，祈祷吧。](https://github.com/dploeger/intellij-api-doc)