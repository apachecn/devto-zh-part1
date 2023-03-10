# npm 安装私有包

> 原文：<https://dev.to/joeymink/npm-install-private-package-8gg>

不是每个人都可以将代码公开提交给 GitHub，并将包发布给 npmjs.com 的 T2。毫无疑问，我们都受益于这样做的人。但是我们中的许多人将这些慷慨的提供应用于(并为其编写新的代码)内部的私有项目。

然而，这并不是开发大型整体内部项目的借口。我声明:模块化和独立包的实现仍然是必要的！保持项目集中。编写独立的、有用的、可重用的包！

> 嗯，好吧，理想主义的傻瓜。HTF:当我不能使用私人套餐时，我应该方便地使用吗？
> ^的否定者

啊，但是你可以！继续读...

[Sonatype](https://www.sonatype.com/) 有一款产品叫 [Nexus](https://www.sonatype.com/nexus-repository-oss) 。这是一个众所周知的 Maven (Java)库，您可以私下运行。但是请注意:它不仅仅是一个私有的 Maven 资源库ðÿ˜，它还是一个私有的 PyPi 资源库...(要说出令人吃惊或高兴的事情)听着...一个私人 npm 库ðÿž‰ðÿ'，它是免费的！

关于为 npm 存储库配置 Nexus 的详细信息可以在[这里](https://help.sonatype.com/display/NXRM3/Node+Packaged+Modules+and+npm+Registries)找到。注意，你可以很容易地设置它来代理 npmjs.com*和*同时托管你的私有包

这对我最近的项目至关重要。我们一直在开发的一个电子应用程序包括许多复杂的、独立的算法，它们应该有自己的项目来存放文档和测试。我可以将这些杂草放在它们自己的 npm 包中，并且已经开始在其他应用程序中重用它们，而不是用杂草中的细节来膨胀电子项目。

如果你的情况允许，请继续张贴你的魔法到 npmjs.com。我们都受益！但如果不能，跑私募回购是必须的！