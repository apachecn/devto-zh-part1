# 0.21 时的晶体状态

> 原文：<https://dev.to/crystal-lang/state-of-crystal-at-021>

*这篇博文最初发布在[官方水晶博客](https://crystal-lang.org/2017/02/24/state-of-crystal-at-0.21.html)T3 上*

随着 0.21 版本[的发布，我们想与你分享今年到目前为止 Crystal 的发展状况，目标是在年底推出](https://groups.google.com/d/msg/crystal-lang/sGxeIxlLKX4/VFIM-iTECwAJ) [1.0 版本](https://crystal-lang.org/2016/12/29/crystal-new-year-resolutions-for-2017-1-0.html)。

首先，我们已经更新了我们的[路线图](https://github.com/crystal-lang/crystal/wiki/Roadmap),不仅仅是为了今年的 Crystal，还包括我们希望在未来使用该语言构建的工具，比如用于轻松编写 Ruby 扩展的完整 DSL，或者桌面 UI 库。我们还改变了针对 Github 问题的[标签方案](https://github.com/crystal-lang/crystal/labels)，并相应地更新了[投稿指南](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md)，以使社区中的任何人都更容易找到如何帮助 Crystal。

关于我们已经确定的 1.0 的关键特性，在社区成员 [lbguilherme](https://github.com/lbguilherme) 和核心团队成员 [bcardiff](https://github.com/bcardiff) 的努力下，我们正在 [Windows 支持](https://github.com/crystal-lang/crystal/pull/3582)上取得稳步进展。今天，Crystal 能够在 Windows 中编译一些程序，最后一个里程碑是支持该平台中的异常。在标准库方面，我们还有很长的路要走，因为每个模块都是在只考虑 UNIX 支持的情况下实现的，所以非常欢迎任何对 Windows 平台感兴趣的人投稿。

下一件大事是[并行](https://github.com/crystal-lang/crystal/tree/thread-support)，在[瓦伊](https://github.com/waj)开始的工作基础上，核心团队成员[吉吉拉尔德兹](https://github.com/ggiraldez)和[胡纳迪](https://github.com/juanedi)正在努力工作。我们很高兴有一个支持多线程的编译器的工作版本，并有一个类似的模型:一个从纤程执行任务的固定线程池，包括偷工作之类的好东西。这方面的工作仍然是实验性的，有相当多的突破性变化需要定义，比如显式线程处理；但是大多数编译器和标准库规范目前都是绿色的。Kemal 的作者 [sdogruyol](https://github.com/sdogruyol/) 甚至设法[在多线程中运行 web 框架](https://twitter.com/sdogruyol/status/833369972919382019)。然而，在测试和性能方面仍有许多工作要做，以确保将工作负载分布在多个线程上所产生的争用不会抵消速度的提高。

我们还开始讨论类型系统，以确保增量编译的可行性。我们已经确定了一些必须解决的泛型和模块即接口的潜在瓶颈，我们将尽最大努力尽快确定所需的突破性变化，并最小化它们的影响。

在沟通方面，我们现在也从我们的官方博客交叉发布到令人敬畏的平台 [dev.to](https://dev.to/) ，你现在可能正在那里阅读这篇文章。[practical dev](https://twitter.com/thepracticaldev)的人超级友好，很快就在网站上为我们建立了一个[水晶郎账户](https://dev.to/crystal-lang/)。Crystal core 团队中的许多人已经关注该网站和 twitter feed 相当长一段时间了，现在看到 Crystal 在平台中有一个房间，我们真的很兴奋。

此外，不破坏惊喜，但我们几乎完成了一个全新版本的网站，有一个更干净的设计。预计很快会在网上看到它。

最后但同样重要的是，我们很高兴本月早些时候在 Google NYC 和 Recurse Center 举办了讲座，并组织了另一次聚会。我们还将于 4 月在三藩市举办首届水晶代码营:[如果您有兴趣加入，请告诉我们](https://docs.google.com/a/manas.com.ar/forms/d/e/1FAIpQLSdN4a-ELm54lZFr_qcD97YLe-OTYnP7vAzMfpQdreCoG4o8_Q/viewform)，或者[如果您想赞助该活动，请联系我们](mailto:crystal@manas.tech)！

我们将定期分享来自水晶之州的新闻，所以请确保关注博客和其他[交流渠道](https://crystal-lang.org/community/)。快乐结晶！