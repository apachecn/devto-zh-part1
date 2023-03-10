# 我终于理解了功能是一种服务

> 原文：<https://dev.to/kahlil/i-finally-understood-functions-as-a-service-2ejc>

自从我第一次听到它，我一直在努力理解像 AWS Lambda 这样的“功能即服务”到底是什么。我听到人们在播客上解释它，并在 AWS Lambda 登录页面上阅读它的内容，但它就是不点击。

上周我和[亨宁](http://twitter.com/hglattergotz)录制了[我们播客反应](http://reactive.audio/87)的最新一集。在那一集，Henning 讲述了他如何使用 AWS Lambda 和 AWS 数据库为他们的工作应用程序构建 API。这让我终于明白这是怎么回事了。

他们通过编写一些代码来构建 API，这些代码解析请求参数，从数据库中检索一些数据，然后以 JSON API 格式将这些数据作为 JSON 发送回来。该代码是作为“服务”提供的功能。

就是这样。

HTTP 层、安全性和可伸缩性都由 AWS 服务提供。 *Functions As A Service* 也就是说你只在使用功能的时候为计算时间付费。当没有对 API 的请求时，你不需要付费。

这是一种难以置信的快速高效的方式来构建一个可以立即投入生产的 API。

在播客中，我们还谈到了越来越多的这些“已解决的问题”,如安全性和可伸缩性，将如何被打包到一些服务中，以及它们的使用将如何在不久的将来非常广泛。

[@codepo8](http://twitter.com/codepo8) 昨天在推特上说的最好:

> 预测:五年后，我们现在称之为“编码”的大部分工作将由机器来完成。他们会做得更好。
> 
> —克里斯·海尔曼([@ code po 8](https://dev.to/codepo8))[2017 年 10 月 23 日](https://twitter.com/codepo8/status/922380136531537921?ref_src=twsrc%5Etfw)