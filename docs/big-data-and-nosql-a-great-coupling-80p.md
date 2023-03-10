# 大数据和 NoSQL:伟大的结合

> 原文：<https://dev.to/kenwalger/big-data-and-nosql-a-great-coupling-80p>

多年来，大数据一直是热门词汇和话题。类似地，NoSQL 作为数据存储和“传统”关系数据库的概念也是一个经常争论的话题。在所有关于这些话题的对话中，问几个问题是很重要的。这个叫大数据的东西是什么？像 OrientDB 这样的 NoSQL 数据库适合大数据吗？让我们探讨这些问题，并了解为什么 NoSQL 确实可以成为适合大数据的**。**

 **### 大数据定义

大数据的一个流行定义是:

> 传统计算能力不足以处理的大量或复杂的数据。

虽然这很有道理，但需要注意的是，大数据的概念在某种程度上是相对的。对于当地的苹果农场主鲍勃来说，它可能与[树顶](http://www.treetop.com/)果汁公司有着非常不同的意义和概念。在技术领域，我们经常会想到与大数据处理相关的脸书或网飞公司。但是每个行业都在获取越来越多的数据。

想想我们的苹果农。他们可能想要收集的一些数据包括土壤湿度、空气和土壤温度、降雨量、种植的苹果类型、树木的地理位置、土壤 pH 值、肥料施用、授粉事件等等。

在数据行业，大数据有四个常见的维度。所谓的 [4 V 的](http://www.ibmbigdatahub.com/infographic/four-vs-big-data)，音量，变化，速度，准确性。

##### 大数据的 4 个维度

*   卷——数据的大小和规模，1tb 或更多的数据通常是一个断点。但即使这样也相对较小。一架波音 737 飞机在美国飞行时会产生大约 240 万亿字节的数据。那些苹果怎么样？
*   多样性——不同形式的数据的多样性。在我们的农业示例中，果园周围可能有不同的传感器向数据库发送各种数据。
*   速度——数据收集和处理的速度。也许不完全适用于 Farmer Bob，但对于许多大数据应用程序来说，速度非常重要。例如，股票交易算法变化非常快，必须及时。
*   准确性——数据本身的质量，是否准确？是否准确存储，能否准确检索？

所有这些因素都有助于影响您是否在处理大数据的决策。

那么，假设我们的[苹果园](https://www.amazon.com/gp/product/0375847448/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0375847448&linkCode=as2&tag=kenwalgersite-20&linkId=628bb05aa0ea2038daa55043d53bec88) [![](img/0043ddf95d5b1434be4f18b1eeae5d07.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=0375847448) 足够大，有资格成为大数据会员，我们为什么要选择 NoSQL 数据库呢？像 OrientDB 这样的 NoSQL 数据库如何帮助解决 4 V 的问题？

### 停止印刷:NoSQL 为了胜利

好吧，在谈论苹果园的时候，用[出版社](https://www.amazon.com/gp/product/B01154QQIY/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01154QQIY&linkCode=as2&tag=kenwalgersite-20&linkId=ec82160d3318edf8f91af407a80f5618) [![](img/65c65fafb7f9a2a1b5a48089c546ff37.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B01154QQIY) 有点双关，请原谅。但说真的，NoSQL 确实为存储大数据提供了一些伟大的胜利。事实上，在 4 V 的许多领域，NoSQL 是比关系型 SQL 更好的选择。

在关系数据模型中，数据的多样性成为问题，因为它需要多个表和许多连接来处理数据。像 OrientDB 这样的多模型数据库能够以无模式的方式存储记录，这种方式易于以编程方式生成。这允许具有各种“外观”的数据快速存储到单个[记录](http://orientdb.com/docs/last/Concepts.html#record)中。作为一个多模式数据库，多种类型的记录可以存储在同一个[数据库](http://orientdb.com/docs/last/Concepts.html#database)中。

此外，由于不需要进行连接，遍历数据进行读取操作的速度非常快。快速读写记录的能力对于跟上数据的速度至关重要。数据库的性能是能够可靠地处理数据并获得结果的重要因素。

SQL 数据库面临的挑战之一是可伸缩性。它们大多是围绕主从架构设计的，这常常成为一个瓶颈。扩展的解决方案是将硬件升级到更大更快。在 NoSQL 世界，我们可以通过多主和分片架构进行扩展。这允许水平扩展，增加了准确性，并且新服务器可以跨地理位置分布。

确保你认为你正在存储的数据实际上正在被存储，这可能是最重要的。有能力和信心知道您的数据库事务是安全和准确的是巨大的。OrientDB [事务](http://orientdb.com/docs/last/Transactions.html)符合 ACID 标准，为您的数据是您的数据提供了额外的保证。这也有助于确保技术部门不会打乱公司高管的计划。

### 包裹

我们已经了解了什么是大数据，以及如何确定您的数据*是否是***大数据。然后我们研究了为什么 NoSQL 非常适合存储我们的数据。为了满足您的大数据需求，OrientDB 在存储您的 NoSQL 数据方面绝对值得一看。**

 *** * *

*在 Twitter 上关注我 [@kenwalger](https://www.twitter.com/kenwalger) 获取我帖子的最新消息，或者在[我的博客](https://www.kenwalger.com/blog)上阅读我的其他帖子。*****