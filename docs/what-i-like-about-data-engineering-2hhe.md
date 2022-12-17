# 我喜欢数据工程的地方

> 原文：<https://dev.to/paulleclercq/what-i-like-about-data-engineering-2hhe>

最近有人问我为什么选择专攻数据工程，我喜欢它的什么。

作为一名开发人员，我更习惯于基于事实和长时间开发的技术问题/文章/推文，记住**为什么**我花了很多时间阅读和开发并回答这个问题感觉真好。

**TL；DR:** 构建值得信赖的分析仪表板，而我认识的更多数据工程师似乎更喜欢构建扩展性好的系统

## 开头

我开始在一家快节奏的广告初创公司担任前端开发/技术支持(几年内从 50 人增加到 500 多人)，处理大量广告 *openRTB* 事件，如点击、查看、完成...通过了解业务实际上是如何运作的，以及客户为什么使用这个平台，我产生了兴趣，[仅仅是因为我希望我的工作对我有意义。](https://www.strikemag.org/bullshit-jobs)

我注意到，除了广泛的出版商网络之外，信任是消费者投资广告活动的首要原因。仪表板是它的关键。延迟、变化或仅仅是错误的指标会发生，并会严重损害业务。不要认为这只会发生在其他人身上，[甚至脸书也一直在他们的广告指标上有“错误”](https://www.forbes.com/sites/greatspeculations/2016/11/17/more-bugs-found-in-facebooks-ad-metrics-to-the-dismay-of-advertisers/#505ddbfe2a85)

在不得不支持几个案例之后，比如与冷酷无情的客户之间的指标差异，这让我了解了指标是如何计算的， *Hadoop MapReduce，Spark jobs* ，这些指标存储在哪里， *Cassandra，Postgres，Hadoop 分布式文件系统*，如果出现问题，我们可以做什么， *Lambda/Kappa 架构*，如何在服务之间发送数据， *Kafka* ，以及数据应该是什么格式， *Avro，Parquet* 。

由于这种学习的意愿，另一家初创公司信任我，让我成为他们的第一位数据工程师。我将永远感谢我在那里学到的一切。我知道我听起来很老套

## 我的建议开始数据工程

数据工程，仍然是软件开发，在这一点上，我越来越想称呼自己为软件工程师/开发人员/你最喜欢的词来描述一个开发人员，而不是数据工程师。
唯一的区别是你可能会在数据库/系统配置文件或文档上花更多的时间，我的一个笑话是我不是数据工程师而是配置工程师，所以**你不要害怕开始，你可以做到。**

## 学习

您可以从今天开始在 [Databricks](https://community.cloud.databricks.com/) 上使用 Scala/Java/Python/SQL 或 R 编写您的第一个 Spark 代码，它提供了一个带有免费 6G 服务器的笔记本平台来分析您最喜欢的数据集，或者使用他们的介绍笔记本。或者使用谷歌的数据仓库 [BigQuery 进行分析，分析像](https://cloud.google.com/bigquery/public-data/) [Github 这样的开放数据集](https://bigquery.cloud.google.com/dataset/bigquery-public-data:github_repos)。

## 了解更多

Maxime Beauchemin (AirBnb/Lyft)写了一篇关于数据工程的漂亮的[文章](https://medium.freecodecamp.org/the-rise-of-the-data-engineer-91be18f1e603)T2 的

我列举了许多看起来新奇复杂的技术， [Martin Kleppman 的《设计数据密集型应用》](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)这本书包含了关于这个主题你需要知道的一切。请记住，本质上它只是简单的配置文件。

现在，我也要问你一个简单的问题

### 你为什么做你做的事？