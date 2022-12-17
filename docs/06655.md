# 谷歌云数据存储初探

> 原文：<https://dev.to/kyle/a-first-look-at-google-cloud-datastore>

注:这是我的帖子关于探索谷歌云平台的[的延续。在](https://dev.to/kyle/a-first-look-at-google-cloudplatform)[我的个人博客](https://kyle.io/2017/07/first-look-google-cloud-datastore/)上也有它的镜像。

* * *

乍一看，Datastore 相当于 DynamoDB。我个人认为 Datastore 比 [SimpleDB](https://aws.amazon.com/simpledb/) 更好。不幸的是，SimpleDB 不接受新客户/ [也没有被部署到新的地区](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)，所以这不是一个好的比较。

也就是说，数据存储背后的基本思想是相同的(托管 NoSQL 数据库)，但它的实现方式是非常不同的。

#### App Engine Datastore vs 云端 Datastore？

曾经，云数据存储曾经是(现在是？)是 App Engine 的一部分，但后来被拆分出来了。据推测，作为这一遗产的一部分，数据存储似乎仅限于 App Engine 所在的地区，不幸的是[并不是谷歌的所有地区](https://cloud.google.com/appengine/docs/locations)。

此外，还为云数据存储创建了一个应用引擎帐户。如果您也使用数据存储 SDK，则需要使用。为什么这种依赖存在，为什么它被暴露是公开的问题。

更令人困惑的是，App Engine Datastore 的文档将 [DB Datastore 列为替代](https://cloud.google.com/appengine/docs/standard/python/storage#superseded_storage_solutions)，但随后链接到关于云 Datastore 的[文档。App Engine 还提到了一个](https://cloud.google.com/appengine/docs/standard/python/datastore/) [NDB 客户端库](https://cloud.google.com/appengine/docs/standard/python/ndb/)，据我所知，它包装了实际的云数据存储服务，但特定于 App Engine。至少还有一篇文章将[云数据存储和 DB/NDB 库作为独立的东西](https://cloud.google.com/datastore/docs/reference/gql_reference?hl=en_US#gql_versions)。

我唯一能建议的是检查网址，确保你正在阅读的文件以`https://cloud.google.com/datastore/`开头。

优点:

1.  类似 SQL 的语义(事务！)
2.  多租户的更细粒度的分解:名称空间/‘种类’/祖先路径(Google 说[种类在功能上等同于表](https://cloud.google.com/appengine/docs/standard/python/datastore/#comparison_with_traditional_databases))。我不确定名称空间/种类的区别是否有用，但这是获得多租户的额外方法，默认情况下被忽略，所以哼。
3.  按请求定价！DynamoDB 是按预期使用量收费的，而不是按实际使用量收费的。鉴于 AWS 对“按使用付费”的痴迷，Dynamo 提供的读/写单元很奇怪
4.  每个属性的自动索引支持任意查询，而不是 AWS，你必须定义任何你想要的索引
5.  允许运行类似 SQL 的查询的仪表板(但仅限于`SELECT`查询)

缺点:

1.  没有什么比 DynamoDB 流更好的了(这对于隐式触发数据更改的复制/异步操作来说非常棒)
2.  与数据存储区相比，Dynamo 在空闲层上的存储容量增加了 25 倍(25GB 比 1GB)
3.  迪纳摩每天提供更多的总读/写操作——如果你有一个稳定的请求率，这是好的，如果你有突发事件，这是坏的
4.  索引(默认创建，您必须选择退出)数据存储似乎是收费的
5.  创建自定义索引需要使用 [`gcloud` CLI 工具](https://cloud.google.com/datastore/docs/concepts/indexes?hl=en_US#deploying_or_deleting_indexes)。在[索引文档](https://cloud.google.com/datastore/docs/tools/indexconfig#Datastore_System_requirements)中没有提到任何其他方法。
6.  如果您有一个涉及过滤多个属性的查询，您可能会遇到这样的情况:内置索引没有覆盖到[，或者受到一个相当长的查询限制列表](https://cloud.google.com/datastore/docs/concepts/indexes#index_configuration)中的[的影响。](https://cloud.google.com/datastore/docs/concepts/queries#restrictions_on_queries)

虽然你可以在 Dynamo 中进行扫描+过滤组合，但 GQL 会用一个`"Your Datastore does not have the composite index (developer-supplied) required for this query."`错误拒绝你。(我的用例是`select * from kind where property1 < value order by property2`。)

我还没有找到让 Datastore 扫描和过滤服务器端的方法，所以我必须迭代所有内容，并在检索后扔掉我不想要的数据。

#### 定价

再多说一点关于价格的问题，因为两款产品的定价模式真的不一样。

如果您每秒处理的请求数量相当一致，那么 Dynamo 的定价模型是有意义的。Dynamo 试图支持猝发，但他们是通过[一个 300 秒的*调配但未使用的读/写容量](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GuidelinesForTables.html#GuidelinesForTables.Bursting)来实现的，然后猝发。当您用完存储桶时，请求会被拒绝。

因此，如果您试图节省资金，将读/写单元减少到 1，并且您做了一些请求量很大的事情，那么除非您在运行操作之前增加这些单元，否则您的日子会不好过。迪纳摩的新自动缩放功能也需要一些时间才能生效(放大警报需要 5 分钟才能生效 CloudWatch 警报设置在`ConsumedWriteCapacityUnits > NN for 5 minutes`)。

相比之下，Datastore 的按请求收费模式更适合变化多端的流量模式，这主要是因为您不必为闲置的容量付费。

如果您正在 Dynamo 中进行任何类型的表扫描，以便按属性查找元素，或者您有单个属性的索引，那么 Datastore 可能会因为默认内置的索引而更好地为您工作。您可以从 Dynamo 获得相同的功能，但它更难设置，并且作为一个单独的表运行(并作为一个单独的表收费)。

如果有复合(多属性)索引，那就有点复杂了。数据存储在隐藏索引复杂性(一旦建立)和实际使用索引方面做得更好。但是设置过程是偶然的，需要你提前知道排序顺序之类的事情。

如果你不做任何花哨的事情，只是通过按键直接访问任何东西，那么 Dynamo 更适合小规模的东西，因为它有更大的可用存储空间(25GB 对 1GB)。