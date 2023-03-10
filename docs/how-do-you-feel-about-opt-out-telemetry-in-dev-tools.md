# 你对开发工具中选择退出遥测有何看法？

> 原文：<https://dev.to/kspeakman/how-do-you-feel-about-opt-out-telemetry-in-dev-tools>

的。自从微软发布了它的最新平台 SDK(.NET Core)，默认情况下遥测是打开的(选择退出)。在没有询问你的情况下，它会将你的使用数据发送回 MS。来自其用户的回应是[压倒性的否定](https://github.com/dotnet/cli/issues/3093)，但 MS 仍然选择这样做。即将推出的 2.0 版本扩展了它收集的遥测数据。

我想知道更广泛的开发社区对他们的构建/开发工具未经请求就发送遥测数据有何看法。表面上是出于最好的理由；修复 bug 并确定最常用和最少使用的功能，以集中精力进行开发。

对我来说，我鄙视选择退出的做法，不管他们的理由或潜在的好处。我希望能够选择加入，而不是不知道数据被泄露到互联网上。在实践中，我研究每个工具，并找出如何禁用遥测技术(如果有的话)。(这已经成为微软平台上的一项繁重工作，操作系统和 SQL 等服务器应用程序现在都选择退出遥测。)即使数据目前正被匿名化并用于正当目的，但这种情况随时都可能改变，或者匿名化可能不充分[1]。数据也可能被黑客窃取[2]或者只是意外地被遗忘在公共场合[3]。

这是我的观点。你怎么想呢?

[1][https://www . the guardian . com/technology/2017/aug/01/data-browsing-habits-brokers](https://www.theguardian.com/technology/2017/aug/01/data-browsing-habits-brokers)
【2】举不胜举的例子
【3】[https://www . the register . co . uk/2007/07/16/internet _ dork _ jihad _ shocker/](https://www.theregister.co.uk/2007/07/16/internet_dork_jihad_shocker/)