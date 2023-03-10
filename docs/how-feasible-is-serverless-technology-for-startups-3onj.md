# 无服务器技术对于创业公司的可行性有多大？

> 原文：<https://dev.to/rohitakiwatkar/how-feasible-is-serverless-technology-for-startups-3onj>

如果你正在与一家初创公司合作，你知道这是一场与时间和金钱的赛跑。我已经为我们的客户评估了许多[无服务器技术](https://www.simform.com/ecosystem-serverless-technologies/)，这些客户希望用无服务器组件构建应用程序。现在有一个新项目摆在我们面前，我们想知道它是否完全符合我们的要求。

由于这是一个简单的应用程序，但具有巨大的扩展潜力，我们正在考虑是采用微服务还是继续使用 monoliths？应用程序将执行用户验证、注册、推送通知和标准 CRUD 操作，但是，它可能会在后台有一个 CRON 向应用程序发送更新。

以下是我们正在寻找答案的一些问题:

1.  根据我的经验，基于服务的架构增加了很多复杂性和通信开销。如果我们有一个 100 名工程师的团队，我们可以将项目分成小块，由一个可管理规模的团队来完成。由于最初的项目团队很小，我不确定微服务是否合适。然而，我们期待着良好的可扩展性选项。你的体验是什么？

2.  无服务器似乎是一个新的领域，用例仍然局限在一个狭窄的范围内。此外，我特别不确定无服务器的性能，以及它如何在快速测试/原型开发环境中运行。冷热启动时间和并发怎么样？ 
    液体错误:内部

    > ![Drew Firment profile image](img/78e0e00651e83e471f190ce06cf7fd9c.png)Drew Firment@ Drew Firment![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ RohitAkiwatkar](https://twitter.com/RohitAkiwatkar)增加分配的内存大小不仅可以提高性能(和成本)，还可以提高函数的一致性和可预测性[bit.ly/2CHepKC](https://t.co/OGudCk5ZSg)21:19PM-02 2018 年 1 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948302640223604741)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948302640223604741)

3.  但最重要的是，我们设想更快的上市时间，并期待通过返回并消除不完善和操作问题来改善我们的 MVP。无服务器有多好？

期待 Dev 社区的看法。让我知道你对这个案子的看法。此外，如果有什么我应该考虑的，请在评论区提到。