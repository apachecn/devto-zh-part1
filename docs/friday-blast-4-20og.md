# 星期五爆炸#4

> 原文：<https://dev.to/horia141/friday-blast-4-20og>

本周有很多新老文章。我想说他们本周倾向于更长，但他们也更像文章，所以他们应该更容易理解。

闪存已死:下一个技术可能是什么？(2017) -了解 StackOverflow 上的问题如何随着时间的推移演变为 Flash，以及其他哪些技术可能会表现出相同的行为。jQuery 和 Ruby on Rails 是 T2 的竞争者。

【flexbox 如何工作-用大的、彩色的、动画 gif 解释(2017) -如题。如果你还没有在你的前端项目中使用 flexbox，你肯定应该使用它。这让事情变得简单多了。它几乎得到了普遍支持。

[Docker 安全最佳实践(2017)](https://blog.sqreen.io/docker-security/?utm_source=cronweekly.com)——我期待的是不要在容器内以 root 身份运行的普通建议。但这涉及到更高级的方法，如图像真实性和使用 Docker Bench 安全性。这里的要点是，虽然 Docker 消除了一些安全问题，但它也添加了许多自己的安全问题。

浏览器用户代理字符串的历史(2010 年) -被提醒用户代理字符串有多疯狂总是很有趣。在这篇文章中使用的仿圣经风格更是如此。

[JavaScript 事件循环:解释(2013)](https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/)-JavaScript 事件循环的快速介绍。虽然有比这个概述更多的内容，但它对来自更传统背景的任何人都是有用的。有趣的事实- [Tcl](https://www.tcl.tk/) 也有类似的概念，它的作者一直致力于推广事件循环作为传统线程并发编程的替代方案。这花了一些时间，但是像`async/await`，事件驱动的网络服务器等等。那项工作的结果。

[在生产中运行 1000 万个 PostgreSQL 索引(并且还在继续)(2017)](https://heap.engineering/running-10-million-postgresql-indexes-in-production/)——这是一篇关于 PostgreSQL 在 [Heap](https://heap.engineering/) 的疯狂使用的巨大文章。对于捕获的每种类型的事件，它们都定义了一个部分索引——一个或多或少带有一个`where`子句的索引。这些是在用户定义和使用事件时以特别的方式创建的。我喜欢的一点是，他们设法将一个业务概念如此巧妙地映射到数据库的一个特性上。

计算机科学的越南(2006 年) -一篇关于 ORM 及其灾难的文章。还有对越南战争以及美国如何遭受其害的相当详细的概述，这本身就是一本有趣的读物。如果你在读完这篇文章后不知道该用什么，试试像 [Dapper](https://stackexchange.github.io/Dapper/) 或 [knex.js](http://knexjs.org/) 这样的微 ORM，它们提供了 ORM 所有有用的部分，但是不要试图接管太多的数据库。库，而不是框架。

[实时数据库调查:Meteor 的架构，RethingDB，Parse&Firebase(2016)](https://medium.baqend.com/real-time-databases-explained-why-meteor-rethinkdb-parse-and-firebase-dont-scale-822ff87d2f87)——这是来自 Wolfram Winegarth 的一篇非常有趣的文章，他在 BaaS 提供商 [Baqend](https://www.baqend.com/) 工作。有链接到经典的 [NoSQL 数据库:调查和决策指导](https://medium.baqend.com/nosql-databases-a-survey-and-decision-guidance-ea7823a822d)和[可扩展流处理:对 Storm、Samza、Spark 和 Flink 的调查](https://medium.baqend.com/real-time-stream-processors-a-survey-and-decision-guidance-6d248f692056)。要点是实时查询，即产生结果流并在数据库发生变化时捕获变化的查询，是非常好的。但是它们很难以可扩展的方式工作，并且大多数解决方案要么不支持大量查询，要么系统不能随着这些查询的写吞吐量而扩展。这也是一种营销手段，所以很明显，Baquend 确实做对了所有这些事情。