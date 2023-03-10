# 在 MongoDB 中使用 R

> 原文：<https://dev.to/kenwalger/using-r-with-mongodb-25g>

R [编程语言](https://www.r-project.org/)是一种用于统计计算的强大语言。在使用统计计算时，经常会出现这样的情况，即所研究的数据来自数据库。R 擅长的一些功能是处理表和矩阵中的数据，以及将列和行连接在一起。这似乎非常适合 SQL 数据库，但是像 [MongoDB](http://www.mongodb.com) 这样的 NoSQL 数据库呢？R 能像分析 SQL 表一样容易地分析一个 MongoDB [文档](https://www.kenwalger.com/blog/nosql/document-model/)吗？

好吧，如果答案是“不”，这篇文章会很短，对吗？因此，让我们看看如何将数据从 MongoDB [集合](https://docs.mongodb.com/master/core/databases-and-collections/)拉入 R。然后，我们将简要地检查一下我们的数据。

### 设置

虽然有适用于各种 ide 的[插件](https://plugins.jetbrains.com/plugin/6632-r-language-support)，比如那些由 [JetBrains](https://www.jetbrains.com/) 开发的插件，但在使用 r 时使用 [RStudio](https://www.rstudio.com/) 还是很常见的。这就是我将在本文中使用的插件，截屏将反映这种用法。在这个过程中，我在 MongoDB 中找到了一个“分数数据库，我们将使用它作为样本数据。我已经把它贴在这里[供下载](http://kenwalger.com/public_html/assets/sample-data.zip)。

我们可以使用 [mongoimport](https://www.kenwalger.com/blog/nosql/mongodb/importing-data-mongoimport/) 轻松地将数据导入到我们的 MongoDB 数据库中。在我的例子中，我将它放入一个名为`kenblog`的数据库和一个名为`scores`的集合中。很有创意，是吧？这里是集合中的一个样本文档的样子:

```
{
   "_id" : ObjectId("5627207b33ff2cf40effc25e"),
   "student" : 2,
   "type" : "quiz",
   "score" : 74
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的集合中有 1787 条记录，分配类型为`quiz`、`essay`或`exam`。让我们看看如何用 r 访问我们的数据。

首先，我们需要获取并加载我们的用于 MongoDB 与 r 接口的包。对于这个例子，我将使用 [RMongo](https://cran.r-project.org/web/packages/RMongo/index.html) ，但是还有另一个可用的包， [rmongodb](https://www.r-project.org/nosvn/pandoc/rmongodb.html) 。遗憾的是，这两个包的 GitHub 库目前似乎都没有太多活动。除此之外，我们仍然可以连接和做一些查询。

### 连接 R 到 MongoDB

我们需要带来我们的包并建立我们的连接:

```
require(RMongo)

mongo <- mongoDbConnect('kenblog', 'localhost', 27017) 
```

Enter fullscreen mode Exit fullscreen mode

在`mongoDbConnect`方法中，我们有数据库名称、服务器名称和我们想要连接的端口号的选项。

接下来，我们将发送一个查询。对于这个例子，让我们只从我们的`scores`集合中获取`exam`数据。为此，我们可以使用`dbGetQuery`方法，该方法接受一个连接对象、集合名称和查询。

```
examQuery <- dbGetQuery(mongo, 'scores', "{'type': 'exam'}") 
```

Enter fullscreen mode Exit fullscreen mode

这将加载类型为`exam`的`scores`集合中的所有记录。让我们用考试分数的值来创建一个向量。

```
exam_scores <- examQuery[c('score')] 
```

Enter fullscreen mode Exit fullscreen mode

不错！现在我们可以利用 R 的一些能力来做一些数据分析。让我们用`summary(exam_scores)` :
简单总结一下我们的数据

```
score       
 Min. : 60.00  
 1st Qu.: 72.00  
 Median : 79.00  
 Mean : 79.45  
 3rd Qu.: 86.00  
 Max. :100.00 
```

Enter fullscreen mode Exit fullscreen mode

干净利落。我意识到这个特殊的例子可以使用 MongoDB 强大的[聚合框架](https://docs.mongodb.com/manual/aggregation/)来计算。然而，有时需要使用外部资源和语言(如 R)进行处理。

### 总结起来

使用 RMongo 包从 R 连接到 MongoDB 非常简单明了。然而，MongoDB 在过去几年中实现的许多新特性并没有包含在 community R 驱动程序中。此外，在这篇文章中，还没有一个“MongoDB 支持的官方 R 驱动程序”。

r 是一种很棒的统计语言，绝对可以用来查询和分析 MongoDB 集合。如果您今天在工作中使用 R，MongoDB 无疑是存储待分析数据的一个选择。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？并得到有益的回应。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F&t=Using%20R%20with%20MongoDB&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi1.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F10%2Ffeature.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Using%20R%20with%20MongoDB)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F&title=Using%20R%20with%20MongoDB)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F&title=Using%20R%20with%20MongoDB)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](//mailto:?subject=Using%20R%20with%20MongoDB&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fusing-r-mongodb%2F)

在 MongoDB 中使用 R 的帖子[最早出现在肯·w·阿尔杰](https://www.kenwalger.com/blog/nosql/mongodb/using-r-mongodb/)的[博客上。](https://www.kenwalger.com/blog)