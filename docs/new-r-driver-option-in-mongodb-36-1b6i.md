# MongoDB 3.6 中新的 R 驱动选项

> 原文：<https://dev.to/kenwalger/new-r-driver-option-in-mongodb-36-1b6i>

在[之前的一篇文章](https://dev.to/kenwalger/using-r-with-mongodb-25g)中，我讨论了在 R 语言中使用 [MongoDB](https://www.mongodb.com) 的一些选项。虽然那篇文章中的信息仍然准确，但 MongoDB 3.6 引入了一个新的 R 驱动程序选项。与前面讨论的驱动程序不同，这个新的 R 驱动程序正在积极开发中。

### 新的 R 驱动

新的 R 驱动程序 mongolite 可以在 [GitHub](https://github.com/jeroen/mongolite) 上找到，并且可以在 Windows 或 OS-X 上使用`install.packages("mongolite")`命令从[综合 R 存档网络](https://cran.r-project.org/web/packages/mongolite/index.html) (CRAN)轻松安装。该驱动程序或客户端由耶鲁安·奥姆斯编写，他也提供了一些 PDF 格式的[精美文档](https://cran.r-project.org/web/packages/mongolite/mongolite.pdf)。让我们进入 RStudio 看看如何使用新的 R 驱动程序。

### 访问 MongoDB

让我们使用与前一篇文章相同的[数据集](http://kenwalger.com/public_html/assets/sample-data.zip)，并使用 [mongoimport](https://www.kenwalger.com/blog/nosql/mongodb/importing-data-mongoimport/) ，将数据放入 MongoDB [集合](https://docs.mongodb.com/manual/core/databases-and-collections/)。数据库再次被称为`kenblog`，集合被称为`scores`。这里也是集合中的一个样本文档:

```
{
   "_id" : ObjectId("5627207b33ff2cf40effc25e"),
   "student" : 2,
   "type" : "quiz",
   "score" : 74
} 
```

在使用了`install.packages("mongolite")`命令之后，我们可以让 R 的力量发挥作用。我们建立到数据库的连接:

```
> require("mongolite")
Loading required package: mongolite
> connection <- mongo(collection = "scores", db = "kenblog", url = "mongodb://localhost") 
```

芒果

*   收藏品
*   
*   全球资源定位器(Uniform Resource Locator)
*   冗长的
*   选择

`collection`和`db`参数允许指定各自数据库信息的名称。`url`是 URI 格式的 mongo 连接字符串。如果您需要来自连接的额外输出，可以将`verbose`布尔值设置为 TRUE。额外的连接`options`，比如 SSL 信息，也可以传入。

建立连接后，让我们查询`scores`集合中的`exam`数据。

```
examQuery <- connection$find('{"type": "exam"}') 
```

这带来了我们的 585 `exam`文档。然后，我们可以创建一个考试分数的向量，并查看它们的摘要。

```
> exam_scores <- examQuery[c('score')] 
> summary(exam_scores)
     score       
 Min. : 60.00  
 1st Qu.: 72.00  
 Median : 79.00  
 Mean : 79.45  
 3rd Qu.: 86.00  
 Max. :100.00 
```

就我个人而言，我已经喜欢上了这个新的 R 驱动程序的语法，它可以用来在 R 中进行查询和使用 MongoDB。

MongoDB 用户熟悉的其他方法有`drop()`删除集合、`aggregate`执行[聚合管道](https://docs.mongodb.com/manual/core/aggregation-pipeline/index.html)操作，以及`insert`在数据库中创建信息。还有许多其他方法可用于 map-reduce 操作以及导入或导出 JSON 或 BSON 数据，等等。

此外，由于支持索引、加密和认证等特性，这个新的 R 驱动程序比以前的选项更加健壮。

### 总结起来

使用新的 R 驱动程序从 R 连接到 MongoDB 非常简单明了。它是新的“官方”和受支持的方法，可以利用 R 的强大功能和 MongoDB 的灵活性和强大功能。

r 是一种很棒的统计语言，绝对可以用来查询和分析 MongoDB 集合。如果您今天在工作中使用 R，这种与 MongoDB 连接的新方式绝对值得一看。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？”并得到有益的回应。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F&t=New%20R%20Driver%20Option%20in%20MongoDB%203.6&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi1.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F10%2Ffeature.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=New%20R%20Driver%20Option%20in%20MongoDB%203.6)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F&title=New%20R%20Driver%20Option%20in%20MongoDB%203.6)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F&title=New%20R%20Driver%20Option%20in%20MongoDB%203.6)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=New%20R%20Driver%20Option%20in%20MongoDB%203.6&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fnew-r-driver-option-mongodb-3-6%2F)

MongoDB 3.6 中新的 R 驱动选项的帖子[首先出现在肯·w·阿尔杰](https://www.kenwalger.com/blog/nosql/mongodb/new-r-driver-option-mongodb-3-6/)的[博客上。](https://www.kenwalger.com/blog)