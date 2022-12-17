# MongoDB 索引简介

> 原文：<https://dev.to/akazia_it/introduction-to-mongodb-indexing>

这篇文章是关于 MonogDB 的数据库索引。我将简要介绍什么是索引以及如何使用它们。

## 了解指标

索引对于高效执行查询非常重要。对于 MongoDB 来说没有区别。让我们假设您有一个很大的文档集合，但是没有索引。为了选择与特定查询语句匹配的文档，MongoDB 必须执行完整的集合扫描。这意味着整个集合中的每个文档都会被读取并检查是否与查询语句匹配。查找单个文档的开销非常大。

游戏的索引来了。索引是存储部分集合数据的特殊数据集。由于数据只是集合的一个子集，读取这些数据变得更加容易。因此，如果您有索引(支持查询的字段)，MongoDB 将使用这些索引来限制必须在集合中搜索的文档数量。动手做一个例子。

## 走过一个例子

首先我们需要一些数据。让我们检索一个[餐馆数据集](https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json)，并将其保存到一个名为 primer-dataset.json 的文件中。你可以用 mongoimport:
进行导入

```
mongoimport --db test --collection restaurants --drop --file ~/downloads/primer-dataset.json 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们运行一个查询并获得一些统计数据。我将利用 explain()方法来获取一些性能数据。在以后的文章中，我会更详细地讨论这个方法。

```
db.restaurants.explain("executionStats").find( { "restaurant_id" : "40356018"} ); 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到相当多的统计数据:

```
 "executionStats" : {
        "executionSuccess" : true,
        "nReturned" : 1,
        "executionTimeMillis" : 25,
        "totalKeysExamined" : 0,
        "totalDocsExamined" : 25359,
        "executionStages" : {
            "stage" : "COLLSCAN",
            "filter" : {
                "restaurant_id" : {
                    "$eq" : "40356018"
                }
            },
            "nReturned" : 1,
             [...] 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们看到整个集合被扫描了(25359 个文档)，花费了 25 毫秒。

## 创建索引

现在让我们在被查询的字段 restaurant_id 上创建一个索引，并重新运行查询。

```
>db.restaurants.createIndex({"restaurant_id": 1})
{
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

参数“1”表示字段 restaurant_id 的索引应按升序排序。
*CreatedCollectionAutomatically*表示操作是否创建了集合。如果集合不存在，MongoDB 会在索引操作中创建集合。
*NumIndexesBefore* 显示命令执行前的索引数
*numIndexesAfter* 显示命令执行后的索引数
*ok* 此时几乎自解释，1 表示成功执行，0 表示失败

## 重新运行查询

在我们对 restaurant_id 设置了索引之后，让我们使用 explain 语句重新运行查询。

```
db.restaurants.explain("executionStats").find( { "restaurant_id" : "40356018"} ); 
```

Enter fullscreen mode Exit fullscreen mode

我们再次得到了相当多的统计数据，我剪下了我们需要检查的部分:

```
 "executionStats" : {
        "executionSuccess" : true,
        "nReturned" : 1,
        "executionTimeMillis" : 1,
        "totalKeysExamined" : 1,
        "totalDocsExamined" : 1,
        "executionStages" : {
            "stage" : "FETCH",
            "nReturned" : 1,
            "executionTimeMillisEstimate" : 0,
            "works" : 2,
            "advanced" : 1,
            "needTime" : 0,
            "needYield" : 0,
            "saveState" : 0,
            "restoreState" : 0,
            "isEOF" : 1,
            "invalidates" : 0,
            "docsExamined" : 1,
            "alreadyHasObj" : 0,
            "inputStage" : {
                "stage" : "IXSCAN",
                "nReturned" : 1,
                "executionTimeMillisEstimate" : 0, 
```

Enter fullscreen mode Exit fullscreen mode

酷！总执行时间降低到 1 毫秒！如您所见，现在只检查了一个文档，而不是 25359 个。我们看到，我们只进行了一次索引扫描(IXSCAN ),而不是读取完整的集合(集合扫描)。
建立一个索引并不局限于一个单独的字段。您可以向 createIndex 语句传递几个用逗号分隔的字段。这将产生一个复合指数。

## 跟踪已创建的索引

现在您已经知道索引有多强大，您可能已经创建了一些。但是你怎么知道野外有什么呢？嗯，只要查询一下:

```
> db.restaurants.getIndexes()
[
    {
        "v" : 2,
        "key" : {
            "_id" : 1
        },
        "name" : "_id_",
        "ns" : "test.restaurants"
    },
    {
        "v" : 2,
        "key" : {
            "restaurant_id" : 1
        },
        "name" : "restaurant_id_1",
        "ns" : "test.restaurants"
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

结果集显示有两个索引。默认情况下创建的 _id 和基于字段 restaurant_id 的另一个 id。如上所述:1 表示索引字段值是按升序创建的。不幸的是，你不能简单地对数据库中的每个字段都建立索引。这可能会大大降低插入、删除和更新等其他操作的速度。
如果对文档执行频繁的插入、删除和更新操作，那么索引需要经常改变，这只是集合的开销。

## 删除一个索引

那么如果你想去掉一个索引该怎么做呢？-放下它..

```
> db.restaurants.dropIndex({restaurant_id:1});
{ "nIndexesWas" : 2, "ok" : 1 } 
```

Enter fullscreen mode Exit fullscreen mode

结果集告诉您哪个索引号受到影响，以及操作是否成功(ok: 1)
您甚至可以使用命令 db.restaurants.dropIndexes()删除所有索引。这将删除除 _id 索引之外的所有索引。

## 索引策略

为您的应用程序构建最佳索引需要考虑几个要点:您有多少(空闲)RAM，您期望的读/写比率是多少，这些是主要的查询..我不知道找到最佳索引的唯一方法，理想情况下，您可以使用接近生产环境的数据集来分析各种索引配置，以了解哪些配置性能最佳。并且合乎逻辑，但是经常被遗忘:如果不再使用某个索引，则删除该索引。

以下索引策略论文发表自 MongoDB:
[创建索引支持你的查询](https://docs.mongodb.com/manual/tutorial/create-indexes-to-support-queries/)
当索引包含查询扫描的所有字段时，索引支持查询。创建支持查询的索引可以大大提高查询性能。

[使用索引对查询结果进行排序](https://docs.mongodb.com/manual/tutorial/sort-results-with-indexes/)
为了支持高效的查询，请在指定索引字段的顺序和排序次序时使用此处的策略。

[确保索引适合内存](https://docs.mongodb.com/manual/tutorial/ensure-indexes-fit-ram/)
当您的索引适合内存时，系统可以避免从磁盘读取索引，您可以获得最快的处理速度。

[创建确保选择性的查询](https://docs.mongodb.com/manual/tutorial/create-queries-that-ensure-selectivity/)
选择性是查询使用索引缩小结果范围的能力。选择性允许 MongoDB 将索引用于与完成查询相关的大部分工作。