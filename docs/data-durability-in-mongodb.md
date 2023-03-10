# MongoDB 中的数据持久性

> 原文：<https://dev.to/kenwalger/data-durability-in-mongodb>

当设计一个数据库时，我们想要确保我们想要存储的数据实际上被存储。数据持久性是应用程序中的一个关键因素。在本地服务器和测试环境中，这通常不是一个大问题。我们可以很容易地判断出我们的环境何时以及是否会崩溃。当我们的系统增长时会发生什么？当我们转移到一个分布式环境中，我们的应用程序有许多不同的部分时，会发生什么呢？

除了 [MongoDB](https://www.mongodb.com) 在最近版本中改进的许多[性能](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-performance-issues/)考虑之外，数据持久性是另一个改进。这也是之前版本的一个话题，产品因此受到了一些关注。让我们来看看围绕数据持久性的概念设计应用程序的一些方法。

### 数据持久性

在处理数据持久性、读取和写入时，我们需要考虑几个不同的场景。让我们来看看这两种不同的考虑。在这样做的时候，我们会看到一些方法来确保我们的系统正在做我们想要它做的事情。

#### 写

MongoDB 中的写操作遵循 MongoDB 中非常清晰的路径。至少理论上是这样。在[副本集](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)的情况下，它们从应用程序被发送到[主](https://docs.mongodb.com/manual/reference/glossary/#term-primary)服务器。数据进入内存存储和[操作日志](https://docs.mongodb.com/manual/reference/glossary/#term-oplog)。此时，默认情况下，服务器向应用程序发回一个“okey dokey”。

请注意，我还没有提到任何关于将数据写入磁盘的内容。还没有发生。然后，主服务器将数据写入[日志](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-storage-engine-journaling/)文件，然后写入磁盘。在此过程中，在数据写入日志后的某个时间点，辅助节点也将数据写入磁盘。

这在许多情况下都很好，因为我们讨论的是应用程序获得“许可”和数据被持久存储到磁盘之间的小时间范围。但是仍然有一些延迟。如果分布式系统在此期间出现问题，就必须采取额外的步骤来获取数据。应用程序认为它在那里，毕竟它确实得到了确认，并且磁盘写入实际发生了什么。

我不打算深入了解在意外关机或故障期间*实际上*在幕后发生了什么。这有点超出了这篇文章的范围。我将展示如何指示 MongoDB 等待向应用程序发送“okey dokey”信号，直到数据确实在磁盘上。

###### 写确认

MongoDB 提供了使用[写关注点](https://docs.mongodb.com/manual/reference/write-concern/)选项设置写操作确认级别的功能。这里有几个不同的选项可供我们选择。

*   我们可以使用`w`选项请求确认数据已经写入副本集中的特定数量的服务器。
*   `j`选项请求确认正在写入日志的数据。这是一个布尔值。
*   还有一个`wtimeout`选项——顾名思义，它设置确认发生的超时时间，以毫秒为单位。

使用`w`选项，我们可以选择告诉 MongoDB 必须确认写操作的服务器的具体数量。或者，有一个方便的“多数”选项，允许在 replia 集的大多数数据承载成员都执行了写操作时进行写确认。

例如，如果我们想在 [mongo shell](https://docs.mongodb.com/getting-started/shell/client/) 中插入一个文档，并在两秒半的超时时间内等待来自副本集的两个成员的响应，我们可以这样做:

```
db.blogs.insert(
   { title: "Data Durability in MongoDB", length: 1099, topic: "MongoDB" },
   { writeConcern: { w: 2, wtimeout: 2500 } }
)
```

###### 使用

那么为什么不总是设置一个写问题呢？主要原因是延迟。必须做出“ok”响应的服务器越多，应用程序获得响应所需的时间就越长。在分布式环境中，物理服务器可能位于某个国家或世界各地。这是应用程序响应能力和数据持久性之间的权衡。

然而，对于应用程序性能和数据持久性来说，一个很好的折衷方案是为您的写操作设置 w: 2。然而，对于必须确认的写操作，选择`w: "majority"`。

#### 读

如何读取我们的数据并确保我们的应用程序拥有最新的数据？我们如何防止脏读，即在数据的内存存储和数据实际写入磁盘之间的时间段内发生的读操作？发生故障时，可能受[回滚](https://docs.mongodb.com/manual/core/replica-set-rollbacks/)影响的读取？

与写问题类似，MongoDB 从 3.2 版本开始提供了一个*读问题*。基于我们对写关注的了解，我们可以推断出[读关注](https://docs.mongodb.com/manual/reference/read-concern/index.html)允许我们指定从副本集中返回哪些数据。选择阅读关注级别时，有三个选项可供选择。

*   `local` -此默认设置返回最新数据，但不保证数据会受到回滚的影响。
*   `majority` -返回已经写入副本集的大多数数据承载成员的数据。
*   `linearizable`—返回反映所有成功写入的数据，这些成功写入在读取操作开始之前确认了“多数”*和*的写入问题。线性化是在[3.4 版](https://www.kenwalger.com/blog/nosql/new-version-new-features/)中引入的，是该版本的另一个重要特性。

脏读似乎是一个大问题。然而，在实践中，我们希望设计我们的应用程序来正确地处理写操作，这样我们就可以消除这些顾虑。但是，有些时候，比如读取密码，确保我们读取的是最新和最持久的数据是至关重要的。

### 包裹

MongoDB 继续倾听社区的声音，解决用户的问题(没有双关语)。以前的数据持久性问题不应该成为不尝试 MongoDB 的理由。

还有来自 [MongoDB World 2017](https://explore.mongodb.com/?page=0&prevItm=349629841&prevCol=648867&ts=71115539) 由[Alex Komyagin](https://www.linkedin.com/in/adkomyagin)on*[read concern 和 WriteConcern](https://explore.mongodb.com/vidyard-all-players/mongodb-world-presentations-regency-c-alex-komyagin-6-21-2017)* 的精彩演讲。我建议您看一下该演讲，了解更多信息和使用案例。

* * *

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下，你可以说“Alexa，问 MongoDB 什么是文档？”并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](https://www.kenwalger.com/blog)上查看原始帖子。*