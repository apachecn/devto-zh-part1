# 黑客新闻首页为我的应用做了什么

> 原文：<https://dev.to/garysieling/what-the-hacker-news-front-page-did-for-my-app>

周日晚上，我为一个副业项目[https://www.findlectures.com](https://www.findlectures.com)发了一个“秀 HN”的帖子:

[![HN Submission](img/9e0e1d02ad03d3d6cb6aa30fdeb322aa.png "HN Submission")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t9H2GMaQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2u0nju1mga2ys01vf2bx.png)

幸运的是，我在 WWDC 报道开始前上了头版。当网站在首页时，我在任何时候都有大约 50 个并发用户。

[![HN Traffic](img/8726c2fdad5f523e8604806e8209809a.png "HN Traffic")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HQ_7Bj2a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ovoall1kk3f1abojy96g.png)

此前，该网站几乎同时出现在 TNW 和 Lifehacker 网站上，流量是 HN 的两倍:

[![TNW and LifeHacker Traffic](img/522428567a5c0b93bfc382895df250af.png "TNW and LifeHacker Traffic")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g-FTsLoI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pzdwg7ey0he8mpoaslkw.png)

从本质上来说，副业往往不会被润色——在这次活动中，HN 的人们报告了大约 15 个问题，包括一个移动设备上的关键 CSS 问题，以及我的 Solr 服务器上的内存不足错误。

在之前的流量高峰期间，我因为能够快速部署更改(基本上，使用 Heroku)而获得了赞赏，因此我能够快速修复这些更改。

自从上一次高峰以来，我一直在利用这个网站来获得更多的反馈，这真的得到了回报。

我有一个“最佳”演讲的电子邮件列表，在注册时，它会询问人们是否有最喜欢的演讲者或感兴趣的话题。

[![Email Signups](img/a0a17fee4d5fcb54c5efa17b89c7783c.png "Email Signups")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qp4H2AEk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7laf532fanupuo7nfo8n.png)

在电子邮件注册中，50%的人填写了反馈表，这比我预期的高了大约 100 倍。从黑客新闻，我收到了大约 75 条信息，推荐我应该索引的发言者。

[![Typeform Image](img/0ef59d8f85537850201825fe1a86101b.png "Typeform Image")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W9GrQH9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z4e1nuy1xo5ppln5syg0.png)

这些笔记大多是积极的。有几个不太像，重要的是要记住，评论者很难通过屏幕产生共鸣，所以有些人会忘记项目背后有真实的人。

[![Typeform Image of a piece of feedback](img/c7f3c9b1537f5fd151afb71abc300742.png "Typeform Image of a piece of feedback")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8xJeYCtb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/56xnpcczsbzhe09l93pw.png)

在我发出的电子邮件中，我还要求对个人电子邮件进行反馈。平均每周有 1%的接受者使用这个。我从报名的黑客新闻读者那里得到了一个 3-4 的笔记，高于正常水平(如果你想复制这个，用 codepen 的这个[牛逼的演示)。](https://codepen.io/cvasquez/pen/oxBvMP)

[![In Email Feedback](img/6a68a50cf918844780b694ec940021eb.png "In Email Feedback")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wZ0p4RPO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oe12u5fy60knuzn7n825.png)

我最近增加了一个功能，可以获得个人视频的反馈。没有办法知道是否有人会使用这个，但在这个流量高峰期间，我很高兴地看到几个人通过不同的路径。

[![Video Feedback](img/ea91cd296a28d49dd20f378a66b2d7bf.png "Video Feedback")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7iTKSabN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wjyd10ul7mxg7fm780w0.png)

[![In Email Feedback](img/b457662b1bd3ee2aa484a4ca3156e074.png "Positive Video Feedback")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1A_AtkmV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/39b9j5bfi8saivc3nu4r.png)

[![Negative Video Feedback](img/d3287aefb490bcbcab097e514c509e73.png "Negative Video Feedback")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UvkoG7wC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zg7r27uamuv53g2h4uqh.png)

我还在谷歌分析中记录人们对视频的了解程度，希望我最终可以训练一个机器学习模型来提高排名:

[![Google Analytics Events](img/d8c72535cb21bcbb48da8742933f1935.png "Google Analytics Events")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iIHM-Hcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uabxboe15jeotctkxgow.png)

最终，HN 首页的效果是一百多条定性反馈，以及一些我可以用来改进网站未来的数据。