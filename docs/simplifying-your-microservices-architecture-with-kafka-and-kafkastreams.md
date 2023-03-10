# 使用 Kafka 和 KafkaStreams 简化您的微服务架构

> 原文：<https://dev.to/danlebrero/simplifying-your-microservices-architecture-with-kafka-and-kafkastreams>

*本文原载于[我的博客](http://danlebrero.com/2017/01/05/proof-of-concept-using-kafkastreams-and-ktables/)T3】*

如果你生活在一个微服务的世界中，你可能会致力于构建非常小的服务，这些服务拥有自己的数据库，其他服务无法窥视，并且将事件发布到一些消息传递基础设施中。

这为您提供了一个良好的松散耦合的架构，其中服务可以来来去去，可以被重写或淘汰，并且可以在不接触工作服务的情况下构建新的功能。

由于其独特的可伸缩性、性能和耐用性，Apache Kafka 经常被选为微服务的消息基础设施。它支持队列和主题语义，如果客户端愿意，可以重放旧消息。

上周，我们开始构建一个这样的微服务，它的工作是:

> 每周向持有任何美国股票交易头寸的客户发送一封电子邮件。

## 平原老解

这是我们传统上用于这种微服务的架构:

[![Architecture diagrams using just Kafka](img/9005a29dae71767890c67bb4e3ca3d20.png "Architecture diagram using just Kafka")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IV1MW0G6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://danlebrero.cimg/kafka-streams-poc-old-solution.jpg)

*   卡夫卡:事件数据的来源。
*   数据库:跟踪每个客户的美国未平仓头寸。当我们消费卡夫卡的新信息时，我们需要保持更新。
*   [动物园管理员的首领选举](http://curator.apache.org/curator-recipes/leader-election.html)或[石英集群](http://www.quartz-scheduler.org/documentation/quartz-2.2.x/tutorials/tutorial-lesson-11.html)，所以只有服务的一个实例发送邮件。

这相当复杂，需要大量代码。

## 卡夫卡溪流’解

Kafka Streams 是一个新的开源库，是 Apache Kafka 项目的一部分，它承诺在不损失其他流处理系统(如 [Storm](https://storm.apache.org) 或 [Spark Streaming](https://spark.apache.org/streaming/) )的功能和可扩展性的情况下，简化流处理。

对我来说，主要卖点是:

*   可扩展，使用与 Kafka 相同的基于分区的模型。
*   实时、可选的微量配料。
*   有状态流处理。
*   它是一个库，而不是框架。

Kafka Streams 不需要任何新的基础设施，只依赖于 Kafka 集群(以及 Kafka 的 Zookeeper 集群，直到 [KIP-90 完成](https://cwiki.apache.org/confluence/display/KAFKA/KIP-90+-+Remove+zkClient+dependency+from+Streams))。

除了一个类似于 Java 8 streams 的功能 API，Kafka Streams 还引入了一个 [KTable](http://docs.confluent.io/3.1.1/streams/concepts.html#ktable-changelog-stream) 的概念。让我们试着解释给定我们的需求，什么是 KTable。

每当一个新的交易头寸被打开或更新时，订单预订系统就向 Kafka 压缩主题发送该头寸的完整状态，使用头寸 id 作为密钥。平仓时会发送一个 *null* 从 Kafka 中删除。

由于我们使用的是压缩主题，Kafka 将只记住为每个键发送的最新值，这意味着我们可以通过重放 Kafka 中存储的所有消息来重建原始表。

所以从概念上来说，我们可以把卡夫卡的压缩主题想象成一个表格:

| 位置 Id(关键字) | 客户 | 心脏 | 数量 | 交换 |
| --- | --- | --- | --- | --- |
| one | 客户端 1 | AAPL | One hundred | 全国证券交易商协会自动报价系统 |
| Two | 客户端 1 | 同ＶIＤＥＯ-ＯＮ- ＤＥＭＡＮＤ | five | LON |
| three | 客户端 1 | 运货单(freight bill) | Thirty-three | 全国证券交易商协会自动报价系统 |
| four | 客户端 2 | AAPL | Twenty-five | 全国证券交易商协会自动报价系统 |
| five | 客户端 3 | 同ＶIＤＥＯ-ＯＮ- ＤＥＭＡＮＤ | Thirty-three | LON |

对于我们的需求，我们需要基本上执行以下 SQL 查询:

```
select distinct(client) from open_positions where exchange = 'NASDAQ' 
```

Enter fullscreen mode Exit fullscreen mode

使用 Kafka Streams 中的 KTable API，看起来像:

```
 (->  (.table  builder  "share-holders"  "share-holder-store")  (.filter  (k/pred  [key  position]  (=  "NASDAQ"  (:exchange  position))))  (.groupBy  (k/kv-mapper  [key  position]  (KeyValue/pair  (:client  position)  #{(:id  position)})))  (.reduce  (k/reducer  [value1  value2]  (set/union  value1  value2))  (k/reducer  [value1  value2]  (let  [result  (set/difference  value1  value2)]  (when-not  (empty?  result)  result)))  "us-share-holders")) 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一个分布式、容错、高度可伸缩的 KTable，它将包含:

| 客户(键) | 位置 |
| --- | --- |
| 客户端 1 | #{1, 3} |
| 客户端 2 | #{4} |

现在我们有了这个 KTable，我们只需要查询它就可以得到我们的客户端列表:

```
(with-open  [all  (.all  (.store  kafka-streams  "us-share-holders"  (QueryableStoreTypes/keyValueStore)))]  (mapv  (fn  [x]  (.key  x))  (iterator-seq  all))) 
```

Enter fullscreen mode Exit fullscreen mode

就在那里！

如果我们看一下该解决方案的架构，它看起来像是:

[![Architecture diagram using Kafka Streams](img/9513b98bf578be0c6a31ac330069e142.png "Architecture using Kafka Streams")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--goWlPfyy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://danlebrero.cimg/kafka-streams-poc-kstreams-solution.jpg)

它比前一个更简单并且具有更少的移动部件。

## 结论

对于这个特殊的用例，Kafka Streams 和 KTables 确实提供了帮助。

首先，代码非常简单，比传统的解决方案简单得多。

第二，我们避免了共享数据库，因此我们的架构中少了一个组件，少了一件需要维护的东西，少了一件可能会损坏的东西。

第三，Kafka Streams 负责工作的分片，每个实例能够为一部分客户准备和发送电子邮件。这带来了可伸缩性，并且消除了额外的协调组件。

最后，它是一个库，所以您可以将它与您喜欢的任何其他库混合，并使用您通常用于部署的任何工具。不需要什么特别的。

当然，如果您查看原始 SQL 语句，您可能会怀疑微服务的成本是否真的值得，但那是改天的问题。

* * *

如果这看起来很有趣，我推荐阅读杰伊·克雷普斯[的精彩介绍](https://www.confluent.io/blog/introducing-kafka-streams-stream-processing-made-simple/)(加上合流博客也很棒)和非常高质量的[卡夫卡流文档](http://docs.confluent.io/3.1.1/streams/index.html)。

你可以在接下来的博客文章的[中阅读实现细节、陷阱并找到一个完整的 Docker 工作环境。](http://danlebrero.com/2017/01/06/proof-of-concept-using-kafkastreams-and-ktables-implementation-notes-gotchas-and-docker-compose/#content)

或者您可以阅读关于使用 Kafka Streams 的 KTables 和 GlobalKTables 进行有状态流处理的[连接。](http://danlebrero.com/2017/05/07/kafka-streams-ktable-globalktable-joining-reference-data/#content)

总而言之，《卡夫卡流》看起来非常精彩，我们肯定会进一步探索。