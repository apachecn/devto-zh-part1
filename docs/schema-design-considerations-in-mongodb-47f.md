# MongoDB 中的模式设计考虑

> 原文：<https://dev.to/kenwalger/schema-design-considerations-in-mongodb-47f>

我之前已经提到了一些好处和一些如何在 [MongoDB](https://www.monogodb.com) 中进行模式设计的例子。在 MongoDB 中建模数据时，一个经常提出的问题是如何最好地处理非关系数据库中的数据模式。我想更深入地探讨 MongoDB 实现的有效模式设计所需的一些考虑因素。

在 MongoDB 中对数据建模时，需要记住的一件关键事情是应用程序将如何使用数据。在设计数据模型时，应该首先考虑数据访问模式。与关系数据库中的数据规范化问题不同，将数据嵌入到文档中通常会为 T2 提供更好的性能。

然而，什么时候决定将文档嵌入到另一个文档中呢？当考虑模式设计时，这样做的一些考虑是什么？

### 关系的类型

在关系数据库世界中，建模不同的关系归结为检查如何建模“一对多关系”和数据的规范化。在 MongoDB 中，有多种方法可以对这些关系进行建模。在 MongoDB 中进行模式设计时，需要考虑的不仅仅是“一对多关系模型”的总括模型。

我们需要为我们的建模考虑“N”的大小，因为在这个例子中，大小很重要。通过将一个文档*嵌入到另一个文档*中，可以轻松处理一对一的关系。但是如果 N 增长会发生什么呢？我们来看看以下案例，“一对少”、“一对多”、“一对吨”。

###### 一对多

即使在关系数据库领域，这也是很常见的事情。需要与相对较少数量的其他数据点相关联的单个记录。比如保存客户信息及其相关的电话号码或地址。我们可以为客户将一组信息*嵌入到*文档中。

```
{ 
  "_id" : ObjectId("56cb1cfb72d245023179fda4"),
  "name" : "Harvey Waldrip",
  "phone" : [
     { "type" : "mobile", "number" : "503-555-5555" }, 
     { "type" : "home", "number" : "503-555-1111"}
  ]
} 
```

这展示了嵌入的好处和缺点。我们可以通过一个简单的查询很容易地得到嵌入的信息。然而，缺点是嵌入的数据不能作为自主数据访问。

###### 一对多

“这里的许多井盖在数量上多达几千个左右。假设我们正在对一个由更小的部分组成的产品进行建模。例如，如果我们有一个[电子零件套件](https://www.amazon.com/gp/product/B01ERPEMAC/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01ERPEMAC&linkCode=as2&tag=kenwalgersite-20&linkId=15383d58ba041dadd79a7be225e2467b) [![](img/3afd554d1f520bcfadfd32a92853e4c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Pi7EUd9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB01ERPEMAC) ，套件中的每个零件都可以作为单独的零件引用。

```
{ 
  "_id" : ObjectId("AAAA"),
  "part_no" : "150ohm-0.5W"
  "name" : "150ohm 1/2 Watt Resistor"
  "qty" : 1
  "cost" : { NumberDecimal("0.13"), currency: "USD" }
} 
```

套件中的每一件都有自己的文档。注意“成本值”的格式，我在一篇关于在 MongoDB 中对货币数据建模的文章中讨论过。在我们的例子中，每一个最终产品或套件都将包含一组对必要部分的引用。

```
{
  "_id" : ObjectId("57d7a121fa937f710a7d486e"),
  "manufacturer" : "Elegoo",
  "catalog_number" : 123789,
  "parts" : [
     ObjectID("AAAA"),
     ObjectID("AAAB"),
     ObjectID("G9D6"),
     ...
  ]
} 
```

现在，我们可以利用一个应用程序级别的连接，或者根据用例使用[lookup](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/)聚合管道操作符来获取关于套件中特定部件的信息。为了获得最佳性能，我们需要确保我们的集合中也有适当的[索引](https://www.kenwalger.com/blog/nosql/mongodb/indexing-mongodb/)。

这种类型的参考允许快速和容易地搜索和更新套件中的部件。它基本上已经成为一种“N 对 N 模式设计”,不需要单独的连接表。相当狡猾。

###### 一对一吨

正如我提到的，“一对多非常适合多达几千个引用。但是如果这还不够呢？此外，如果引用引起了关于 16MB 文档限制的模式设计问题，该怎么办？这就是[父引用](https://docs.mongodb.com/manual/tutorial/model-tree-structures-with-parent-references/)变得非常有用的地方。

让我们想象一个事件日志的情况。我们将有一个主机文档，并将该主机存储在日志消息文档中。

**主持人**

```
{ "_id" : "Bunyan", 
  "name" : "logger.lumberjack.com", 
  "ip_address" : "127.55.55.55"
} 
```

**消息**

```
{ "_id" : "MongoDB", 
  "time" : ISODate("2017-08-29T17:25:00.000Z"),
  "message" : "Timber!!!", 
  "host" : ObjectId("Bunyan")
} 
```

同样，为了优化搜索，我们希望确保索引的位置正确。

### 模式设计–主要考虑事项

既然我们已经看到了一些模式设计选项，我们如何确定哪一个是最好的呢？在选择之前有一些事情需要考虑，这已经成为在 MongoDB 中进行模式设计时要问的标准问题。

##### MongoDB 模式设计的黄金法则

1.  除非有令人信服的理由，否则最好选择嵌入。
2.  需要单独访问一个对象是**而不是**将该对象嵌入另一个文档的一个令人信服的理由。
3.  无限制的数组增长是一个糟糕的设计。
4.  不要害怕应用程序端的连接。正确索引的集合和查询可以产生高性能的结果。
5.  在对数据进行反规范化时，要考虑应用程序的读写比率。
6.  最后，如何对数据建模取决于应用程序的数据访问模式。将模式设计与应用程序读写数据的方式相匹配。

### 总结起来

有一些很好的参考资料可以用来在 MongoDB 中设计模式。我最喜欢的一些是 [MongoDB 应用设计模式](https://www.amazon.com/gp/product/1449340040/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1449340040&linkCode=as2&tag=kenwalgersite-20&linkId=bfb88e234ef071bcb810e71b49cc497c) [![](img/c396d8ab12e1a9f9c1961a35f55e334b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BJCDaLCX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D1449340040) 和[MongoDB in Action](https://www.amazon.com/gp/product/1617291609/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1617291609&linkCode=as2&tag=kenwalgersite-20&linkId=b97339aeb20f8a57ca1bc1bfed1baf2a)[![](img/918ae6ba5835f8e360d4c7f1cb7adba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TdW9CSHB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D1617291609)。虽然我没有看过或读过它，但这本小小的 Mongo DB 模式设计书 [![](img/18122f1af9c14fd1b8aecd54fd9dec66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sDjWlRK1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D1517394023) 看起来也是一个很有前途的资源。

胡安·罗伊也有一个关于这个主题的不错的[幻灯片](https://www.slideshare.net/juanroycouto/mongodb-schema-design-tips-tricks-79266638)。绝对值得一看。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问一下 MongoDB 什么是文档？并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。*

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F&t=Schema%20Design%20Considerations%20in%20MongoDB&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi0.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F08%2Fmain_feature-e1504058462711.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Schema%20Design%20Considerations%20in%20MongoDB)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F&title=Schema%20Design%20Considerations%20in%20MongoDB)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F&title=Schema%20Design%20Considerations%20in%20MongoDB)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Schema%20Design%20Considerations%20in%20MongoDB&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-design-considerations-mongodb%2F)

MongoDB 中的[模式设计考虑首先出现在 Ken W. Alger](https://www.kenwalger.com/blog/nosql/mongodb/schema-design-considerations-mongodb/) 的[博客上。](https://www.kenwalger.com/blog)