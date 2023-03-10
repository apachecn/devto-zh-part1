# 解决架构债务:我们如何取代生产弹性搜索集群

> 原文：<https://dev.to/jwg2s/tackling-architectural-debt-how-we-replaced-a-production-elasticsearch-cluster>

[![](img/a4865de055a3c985174e2ea91261b45a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9A9mEc4s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2ADfqXDH9LoofmHsRhALEqMQ.jpeg)

随着应用程序数据的数量和复杂性随着任何新兴企业的发展而增长，我们都会遇到性能和可扩展性问题。一些问题可以通过对现有基础设施的轻微调整来解决，而另一些问题则需要对系统架构进行根本性的改变。

在 [Contactually](https://www.contactually.com/about/careers/) 时，一个这样的例子是处理一个不健康的弹性搜索集群，该集群遭受可用性问题、同步问题和数据流通过我们的系统的基本低效。

本文将主要关注我们在 2016 年 6 月的 [Contactually](https://www.contactually.com/about/careers/) 所做的大规模架构变更，以加强不可靠的弹性搜索集群。

### 为什么我们在接触时使用弹性搜索

> [Elasticsearch](https://www.elastic.co/products/elasticsearch) 是一个分布式的、> RESTful 搜索和分析引擎，能够解决越来越多的用例。作为弹性堆栈的核心，它集中存储您的数据，因此您可以发现预期的数据，发现意外的数据。

在 [Contactually](https://www.contactually.com/about/careers/) ，我们使用 Elasticsearch 让我们的用户可以通过众多搜索标准快速筛选他们的联系人，例如姓名、电子邮件地址、邮寄地址、电话号码、邮政编码，甚至用户创建的自定义字段。速度是关键——我们的许多用户都有成千上万的联系人。

我们在规范化的关系数据模型中存储了数十亿个数据点。在本文中，我不会深入讨论规范化的利弊，但是在设计任何 T2 高通量系统时，它们都是非常重要的。

#### 非规范化文档存储

想象一下，试图通过可能在 10 个表中的任何一个中的属性在数据库中查找记录。这可能需要非常昂贵的连接，尤其是当您处理的数据规模达到数十亿条记录时。

[存储在非规范化模式中的规范化数据示例](https://www.anchormen.nl/elasticsearch-as-a-pentaho-source-through-sql4es/)

当使用 Elasticsearch 或任何其他分布式搜索服务时，您可以构建有意非规范化数据的索引模式，以便可以快速查询数据，而不需要在大量数据库表之间进行昂贵的连接。

然而，这有一个明显的缺点，那就是你的“真实来源数据库”中的任何数据变化都必须被快速复制并索引到你的 Elasticsearch 集群中。对于在 [Contactually](https://www.contactually.com/about/careers/) 的我们来说，这通常不会超过 50-100ms，但也取决于数据变化的频率和您的特定用例。

#### 分布式存储

Elasticsearch 最大的好处之一是集群存储文档的分布式特性。集群中的每个节点都有一定数量的碎片。

Elasticsearch 使水平扩展变得简单——当一个新节点添加到集群中时，Elasticsearch 会自动将碎片复制到新节点，以便重新平衡集群。

冗余是工程的基本原则——每个碎片至少存储在两个节点上(取决于配置)。更重要的是，每个碎片及其副本都存储在不同的节点上。这确保了如果一个节点失败，主碎片和它的副本不在同一个节点上。这避免了任何单点故障。

### 自我管理集群的经验教训

我们已经了解了为什么您可能会在您的应用程序中使用 Elasticsearch，但是我们还没有了解管理集群本身的复杂性。我们的第一个 Elasticsearch 实现是在带有 AWS 的 EC2 实例上用大量节点建立的。

#### 专注于您的产品，而不是管理您的集群

对于一个小型工程团队来说，当我们真正需要关注最终用户的体验时，我们不想花费宝贵的时间和资源来进行集群管理——快速查询、复杂查询和使用 [ngrams](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-ngram-tokenizer.html) 的部分字符串匹配等。

一个示例集群

#### 没有内部专家，很可能会失败

由于我们最初的 Elasticsearch 实现是使用带有 AWS 的 EC2 实例自托管的，所以我们有各种各样的可靠性问题。更糟糕的是，我们直接从 Elasticsearch 向客户提供数据。如果我们的 Elasticsearch 索引与我们的数据库不同步，我们将向用户提供陈旧的数据，而用户希望数据能够接近实时更新。

#### 基本工程原理是必不可少的

糟糕的是，我们没有容错功能。当 Elasticsearch 集群宕机时，用户认为我们丢失了他们所有的数据。这显然降低了人们对我们品牌的信任度——这可能是最大的支出。

不管你在一个新的团队或公司继承了什么系统，对所有系统的审计和分析对于识别技术债务和优先处理哪个债务是至关重要的。

### 创可贴——使用 Postgres

最直接的收获是宁愿慢而正确，也不愿快而错误——尤其是当核心用户体验岌岌可危的时候。我们的游戏计划包括几个关键步骤:

*   简化我们暂时提供给用户的搜索功能
*   使用 Postgres 搜索联系人，知道[我们需要更多的 IOPS 来处理增加的 Postgres 负载](https://medium.com/contactually-engineering/postgres-at-scale-query-performance-and-autovacuuming-for-large-tables-d7e8ad40b16b)
*   从堆栈中删除 Elasticsearch 集群，包括所有 EC2 实例和索引

这种方法有两个好处:1)尽管我们速度较慢，但我们绝不会给用户留下所有数据都不见了的印象；2)通过构建我们搜索功能的 Postgres 版本，如果我们的下一个 Elasticsearch 实现崩溃了，我们可以为自己设置现成的容错功能。

### 让我们再试一次——重新添加弹性搜索

一旦我们把创可贴放好，我们的用户对我们向他们证明他们的数据不会丢失的能力更有信心，是时候重新实施 Elasticsearch 了。我们希望记住一些核心工程原则:

*   **高可用性** —如果集群崩溃，我们需要一个备份，以便能够自动进行故障转移。
*   **无单点故障** —如果 Elasticsearch 集群及其备份出现故障，我们可以回退到功能集减少的 Postgres 搜索。
*   * *没有自我管理的集群** —我们选择了 elastic.co 的来托管我们的集群，并自动执行所有集群管理(包括为批量索引操作放大和缩小集群，例如，当模式改变时)。

### **对方的快速移动和破事**

在创业初期，人们会走很多捷径。如果你放慢脚步，以正确的方式做每一件事，有人可能会抢在你之前进入市场，获得关键的市场份额。然而，随着公司的成熟，偿还技术债务的时间将会到来。

这只是我们在[接触到的](https://www.contactually.com/about/careers/)处理的项目中的一个例子，这些项目告诉我们如何着手新项目——在实施关键系统时记住最佳工程实践。

你或你的公司是否经历过类似的成长之痛？请大家评论，分享经验！

* * *

对数百万专业人士来说，人际关系是一个可行企业的支柱。无论你是在与客户、潜在客户还是潜在投资者合作， [Contactually](https://www.contactually.com/about/careers/) 帮助你与那些能让你成功的人建立更牢固的关系。

[Contactually](https://www.contactually.com/about/careers/) 正在寻找有经验的软件工程师，他们热衷于用代码解决复杂的问题。我们已经采取了一种新颖的方法来构建商业软件——关注最终用户——而且一直很有效！我们的用户爱我们。来帮助我们构建一个产品，使联系人管理变得简单，并将 10，000 人从笨重、过时的软件中解救出来。