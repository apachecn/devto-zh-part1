# 新版本...新功能

> 原文：<https://dev.to/kenwalger/new-version-new-features>

如果你涉足软件开发行业，甚至仅仅使用一台计算机，你就会知道供应商非常频繁地推出他们软件的新版本。似乎当你刚刚掌握了第 10 版，第 11 版就推出了，学习曲线又得从头开始。我很固执，我个人还在用微软 Office 2003...我知道，别笑。

有时新版本除了漏洞修复和额外的麻烦之外，什么都不会提供。其他时候，新的软件版本包含了各种各样立即有用的新功能。2016 年 12 月发布的最新版本[MongoDB](http://www.mongodb.com)3.4 就是如此。

我想看看他们在这个面向开发者的最新版本中包含的一些新特性。有几个新特性更适合从管理员的角度来讨论，比如 [zones](https://docs.mongodb.com/manual/tutorial/manage-shard-zone/) 、[elastic scalability](https://docs.mongodb.com/manual/core/sharding-balancer-administration/)增强、[一致性控制](https://docs.mongodb.com/manual/reference/read-concern/)，以及对数据库访问的[安全性](https://docs.mongodb.com/manual/release-notes/3.4/#security-enhancement)的改进。对于这篇文章，让我们继续讨论一些面向开发的特性。

[3.4 版](https://www.mongodb.com/download-center?jmp=nav#community)在数据处理和聚合、国际语言考虑和第三方应用程序连接工具方面提供了一些出色的改进。

他们通过新的 [$graphLookup](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/) 聚合阶段将图形处理能力直接引入 MongoDB。这个新阶段允许 MongoDB 基于与起始文档的特定关系递归查找文档。当然，这个特性本身有很多用途，比如绘制社交网络、商业实体、家谱等等。现在可以在 MongoDB 中利用图形数据库的能力。对于那些使用单独图形数据库的人，如 [Neo4j](https://neo4j.com/) ，他们现在可以使用单个 MongoDB 数据存储来处理他们的数据，并获得图形以及其他业务关键数据。随着全球互联应用的兴起，能够绘制关系图的需求越来越大。

此外，随着应用程序的全球化，为您的应用程序提供本地化语言考虑变得越来越重要。我家刚刚接待了一名来自德国的交换生，让我们知道德国电话簿和德国字典对单词的分类不同。如果能够解释这些语言规则和细微差别，那就太好了。MongoDB 3.4 支持 100 多种不同的语言和地区。可以根据[集合](https://docs.mongodb.com/manual/reference/glossary/#term-collection)、[索引](https://docs.mongodb.com/manual/indexes/)或[视图](https://docs.mongodb.com/manual/core/views/)指定一个[排序](https://docs.mongodb.com/manual/reference/collation/)。此外，一些操作也支持排序，例如[查找](https://docs.mongodb.com/manual/reference/method/db.collection.find/)、[聚集](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/)和[更新](https://docs.mongodb.com/manual/reference/method/db.collection.update/)。

另外几件让我个人感到兴奋的事情是对 BI 的 [MongoDB 连接器](https://www.mongodb.com/products/bi-connector)和 Apache Spark 的 [MongoDB 连接器](https://docs.mongodb.com/spark-connector/current/)的更新。作为一个喜欢可视化数据的人，BI 的连接器非常令人兴奋。通过将更多的查询执行转移到 MongoDB 进程中以减少延迟和带宽，性能得到了提高。围绕 BI 连接器的其他改进包括[安装](https://www.mongodb.com/download-center#bi-connector)的简化、配置过程、授权和对 Windows 的支持。

Spark 连接器得到了一些很好的关注，同时也进行了更新，以支持最新的 [Spark 2.0 版本](http://spark.apache.org/)。对于熟悉 Spark 的人来说，连接器允许我们访问 Spark 的 Scala、Java、Python 和 R 库，并将 MongoDB 数据作为数据帧和数据集引入。这使我们能够利用 MongoDB 中已有的数据，通过 Spark 的机器学习、图形、流和 SQL APIs 工具进行分析，并为数据科学家和工程师提供更短的周转时间。

3.4 中还有其他几个新特性，比如[分面导航](https://docs.mongodb.com/master/release-notes/3.4/#new-aggregation-stages-for-faceted-search)，对[聚合管道](https://docs.mongodb.com/master/release-notes/3.4/#aggregation)的增强，以及对[十进制数据类型](https://docs.mongodb.com/master/release-notes/3.4/#decimal-type)的支持。在之前的帖子中，我已经提到了对 [MongoDB Compass](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-compass-an-overview/) 及其 DBaaS 产品 [Atlas](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-atlas/) 的一些改进。所有这些新的和/或增强的特性中有许多令人兴奋的东西，使得 3.4 版值得进一步研究。

我很高兴能够继续研究各种可能性，尤其是围绕$graphLookup、多语言排序和各种连接器。我知道不是每个组织都在一个地方并且能够升级到今天的 3.4，但它绝对值得一看。你可以在他们的下载中心[这里](https://www.mongodb.com/download-center?jmp=nav#community)下载。

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](https://www.kenwalger.com/blog)上查看原始帖子。*