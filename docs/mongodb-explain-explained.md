# MongoDB explain()解释道

> 原文：<https://dev.to/kenwalger/mongodb-explain-explained>

在 [MongoDB](https://www.mongodb.com) 中运行查询时，需要考虑许多不同的因素。运行 [find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/) 操作时，在 [mongo shell](https://docs.mongodb.com/getting-started/shell/client/) 中使用的一个有用的东西是使用 [explain()](https://docs.mongodb.com/manual/reference/method/cursor.explain/) 方法。在这篇博文中，我将看看 explain()的一些选项以及结果的含义。

### 解释()

正如之前关于 MongoDB 中的[索引](https://www.kenwalger.com/blog/nosql/mongodb/indexing-in-mongodb/)的文章中所讨论的，我们可以使用 explain()方法来了解所选择的查询计划。这允许检查给定查询的性能。它可以以下列方式使用:

```
db.collection.find().explain() 
```

Enter fullscreen mode Exit fullscreen mode

生成的信息可用于查看查询使用了什么索引，查询是否是覆盖查询，以及查询针对的是[分片集合](https://docs.mongodb.com/manual/sharding/)中的哪些服务器，等等。

可以利用三种不同的详细模式来确定所提供的信息量。

##### 详细模式

*   **query planner**——find()方法中提供的给定查询通过[查询优化器](https://docs.mongodb.com/manual/core/query-plans/)找到最有效的查询。然后，这个“获胜计划”被传递给[查询计划器](https://docs.mongodb.com/manual/reference/explain-results/#explain.queryPlanner)，并为评估后的查询返回信息。查询不在此模式下运行。结果，像查询时间这样的事情，例如`executionTimeMillisEstimate`是真实的估计，因为查询还没有被执行。
*   **executionStats** -在此模式下运行时，查询优化器将运行，查询将完全执行。返回的信息详细说明了在该特定查询中实际发生的结果。
*   **all plans execution**——顾名思义，这个模式返回所有可能的查询计划的信息。在执行获胜计划并为其返回统计数据的同时，还会返回其他候选计划信息。这是 explain()的默认模式。

这些不同模式提供的各种信息可能非常有用。让我们看一看 explain()返回的一些结果，并浏览它们显示的内容。

### 结果

对于这个例子，我将使用一个博客的测试示例数据库。该数据库包含两个集合，用户*T2 和文章*T4，并且运行在一台非共享的机器上。每个集合大约有 550，500 个文档，并且没有超出`_id`的索引。**

让我们先来看看对单个用户名的查询会返回什么。并且看一看所提供的一些零碎信息。

```
db.users.find( { "username": "User_9"} ).explain() 
```

Enter fullscreen mode Exit fullscreen mode

[![explain output](img/d44abfd80893a9d58796102ab46a0263.png)T2】](https://www.kenwalger.com/blog/wp-content/uploads/2017/05/basic_find_with_notes-1.png)

parsedQuery 部分是我们正在探索的查询。query*阶段*提供了对获胜计划的操作类型的描述。它可以是下列之一:

*   **COLLSCAN** -表示对查询进行了集合扫描，这意味着查询查看了**每个**文档以获得结果
*   **IXSCAN** -表示查询使用了一个索引
*   **获取** -用于检索文档
*   **SHARD_MERGE** -从碎片中合并数据的结果

阶段是一个树形结构，可以有多个子阶段。查询的*方向*显示了查询是按正向还是反向顺序执行的。 *serverInfo* 部分显示了查询所针对的服务器的信息，并且在 *version* 键中包含了 MongoDB 数据库的版本。如果集合在一个分片的环境中，那么每个被访问的分片都会列在 *serverInfo* 中。

当使用“executionStats”详细模式运行命令时:

```
db.users.find({ "username": "User_9"} ).explain("executionStats") 
```

Enter fullscreen mode Exit fullscreen mode

附加信息是作为对数据运行查询的结果而提供的。

[![explain with executionStats](img/83da96e5859291b2db411f8d81d7e86e.png)T2】](https://www.kenwalger.com/blog/wp-content/uploads/2017/05/executionStats_with_notes.png)

在这里，我们看到了运行查询所用的时间，以及返回了多少文档，`nReturned`，以及数据库检查了多少文档，`totalDocsExamined`。正如我在关于[索引](https://www.kenwalger.com/blog/nosql/mongodb/indexing-in-mongodb/)的帖子中提到的，理想情况下，这两个数字应该非常接近同一个值。

### 包裹

使用 explain()方法时，可以获得很多信息。它提供了一些关于查询实际上是如何运行的重要信息，并指出集合在哪里可以从索引中受益。在使用其他 [MongoDB 工具](https://www.kenwalger.com/blog/nosql/mongodb-cli-tools/)之前，这应该是您检查慢速查询的第一站。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](https://www.kenwalger.com/blog)上查看原始帖子。*