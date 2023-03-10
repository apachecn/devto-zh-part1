# 使用 Webtask.io 和无服务器的快速 API 原型。

> 原文：<https://dev.to/foxteck/fast-api-prototyping-with-webtaskio-and-serverless-43i>

[![](img/86a33afa4e3b243502f7af72a72a1609.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0_LuNBGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6lR-YCYovFX1ZujxZYjpAg.jpeg) 

<figcaption>照片由[【Shiro hatori】](https://unsplash.com/photos/WR-ifjFy4CI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

拍摄

功能即服务(FaaS)通常被称为*无服务器计算*，感觉像是每个工程师祈祷的答案。简单的功能，只做一件小事，运行在可以自动扩展的基础设施上，只需很少的费用，甚至可以免费使用ðÿ˜！

[**Webtask**](https://webtask.io/) 是 Auth0 团队的功能即服务实现。它非常适合原型制作，并且易于上手。他们上周刚刚发布了他们的无服务器集成。

我正在和 [**无服务器**](https://serverless.com) 一起做我目前的项目，我想尝试一下 Webtask 已经有一段时间了，所以我决定这是它的最佳时机。

在几分钟内，我有了一个 REST API，在 Express、RethinkDB、Webtask 和 Serverless 的帮助下部署并运行。

### ðÿ入门

安装无服务器框架并创建新项目: