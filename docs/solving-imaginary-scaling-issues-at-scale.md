# 解决假想的缩放问题(按比例)

> 原文：<https://dev.to/ben/solving-imaginary-scaling-issues-at-scale>

我是 [dev.to](https://dev.to/) 的创始人和站长，这是一个程序员写类似这样的文章、进行讨论和交朋友的平台。开发应用程序和发展业务是一件非常有趣的事情。在技术方面，随着应用程序变得越来越复杂，我们试图让事情变得简单，但因为我们有目的地构建了一个非常简单的解决方案，并且在这个过程中没有遇到任何严重的扩展问题。

我们经常处理的挑战围绕着交付正确的用户体验，保持我们的重点沟通，以及从日常生活中选择正确的挑战。我们的团队有五个开发人员和一个主要关注业务方面的人，尽管我们都尽可能地凑钱。

除了发展平台和我们的沟通结构这些非常现实的问题之外，我真的很喜欢思考未来潜在问题的解决方案。我通常在周末这样做，那时候我不需要向任何人解释我的时间使用情况，并不是说我们一开始就很重视这个。所以现在，我正在探索将我们的数据库分片并从世界各地的多个地区为核心应用程序提供服务的想法，以减少当请求到达原始服务器时数据库读取的延迟。我已经做了研究，选择了我们可能使用的库来实现这一点，并且总体上考虑了如何实现我们的解决方案。

但是这是一个我们没有的问题的解决方案，所以我们不会实施其中的任何一个。我就知道会这样。

这是一个练习，而且是一个非常私人的练习。最终，我们可能需要作为一个团队开始沿着这些路线思考，我对我们这样做感到兴奋。但是现在，我们将继续努力为我们的问题建立简单的解决方案，并利用我们的想象力来提出新的功能和优化，以取悦我们的用户。

> ![unknown tweet media content](img/885a4f42413fffff5e37972178590771.png)![The Practical Dev profile image](img/fbf25e6c8920b0aa54c6073846fc627c.png)实用 Dev[@ thepracticaldev](https://dev.to/thepracticaldev)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)第一章:名字很酷的数据库2016 年 11 月 21 日下午 17:27[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=800752571497545729)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=800752571497545729)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=800752571497545729)

目前，我们有一个非常好的单片应用程序，很好地利用了我们的 CDN，我们的用户感谢我们在性能优化方面所做的努力。大概就是这样。