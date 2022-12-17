# 更改流，即将在 MongoDB 3.6 中推出

> 原文：<https://dev.to/kenwalger/change-streams-coming-soon-in-mongodb-36-967>

对信息的期望不是即时访问数据的日子已经一去不复返了。在当今世界，人们期望实时的结果和信息。葡萄藤周围土壤的含水量是多少 X 右**现在**？不是昨天，不是五分钟前，而是现在。在当今的许多应用程序中，得到不断变化的数据点的通知是至关重要的。在即将发布的[MongoDB](http://www.mongodb.com)3.6 版本中，引入了一个新特性来帮助实现这些实时期望:变更流。

### 改变流定义

在驱动程序中实现变更流，在 API 中使用新的[聚合](https://docs.mongodb.com/master/aggregation/)操作符、`$changeStream`和`watch`方法。我们现在可以在管道的开始处指定一个`$changeStream`阶段，并请求针对特定集合的特定更改发送通知。

### 示例用例

让我们以我之前提到的土壤湿度为例。想象一下，你有一个农场，有多个[湿度传感器](https://www.amazon.com/gp/product/B06ZYVBRND/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B06ZYVBRND&linkCode=as2&tag=kenwalgersite-20&linkId=2e3515c3da625962cfafe8e820620418) [![](img/b4df6bb4ca0120af7acc8dae7c215b4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CgQJW5vj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB06ZYVBRND) 或一个 [TMP36 模拟温度](https://www.amazon.com/gp/product/B011NEQ7J4/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B011NEQ7J4&linkCode=as2&tag=kenwalgersite-20&linkId=c8ee5dcc279985c2897c08b0ef5a504f)传感器 [![](img/f801c70c886839571669395beabca36a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OY81OJmn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB011NEQ7J4) 连接到类似于[节点 MCU ESP8266 ](https://www.amazon.com/gp/product/B010O1G1ES/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B010O1G1ES&linkCode=as2&tag=kenwalgersite-20&linkId=85c3b431dbcb2773387af28ff09a6e4f) [![](img/cab8335bb1725e19852a510c1175f9a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4t5fSGvv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB010O1G1ES) 或 [LoPy](https://www.amazon.com/gp/product/B06W9GBL2W/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B06W9GBL2W&linkCode=as2&tag=kenwalgersite-20&linkId=11f52ad3f601e4e50c7a7799699da948) [![](img/55cf9f1704d26e5b574b84fec4c19443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g6Pz2eZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB06W9GBL2W) 设备的东西。在不详细介绍如何设置物联网网络的情况下，为了我们的用例，我们可以假设他们正在将读数发送回 MongoDB 服务器。也许这些数据甚至被储存在 [MongoDB Atlas](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-atlas/) 上。不过，如果您有兴趣了解更多信息，我以前写过关于带温度传感器的 LoPy 微控制器和 ESP8266 的文章。

无论如何，为了便于论证，假设我们需要知道土壤的含水量何时下降到某一水平以下。类似设备上的水分值是作为 0-1024 之间的原始数值测量的。数字越高，土壤中的水分越多。对于我们的用例，我们需要知道湿度水平何时降到 350 以下(太干)。

###### 变化流在行动

由于这些记录经常变化，我们可以设置一个变化流来获得通知。用`watch`方法建立通知相当简单。我们可以这样设置:

```
change_stream = client.winery.collection.watch([
    {'$match': {
        'operationType': {'$in': ['insert']}
    }},
    {'$match': {
        'moisture_level': {'$lt': 350}
    }}
]);

for change in change_stream:
    turn_on_water();
    # Something else magical 
```

现在，当我们在数据库中插入一个低于 350 的`moisture_level`时，我们将打开我们的浇水系统以及任何其他需要做的事情。我们可以建立一个类似的变化流，当湿度水平足够高时，保证在 450 关闭系统。

这只是变更流成为有用工具的一个例子。动态更新关于数据变化的报告、发布/订阅类型的功能、代理和许多其他用例现在对本地 MongoDB 操作开放。

### 更改蒸汽详细信息

你会注意到那里有一个`operationType`正在被观察。更改流查看五种不同的事件或操作类型

*   插入
*   删除
*   替换(除唯一 id 之外的所有内容)
*   更新
*   Invalidate(在返回无效光标的情况下)

变更流也利用访问控制，并且可以基于[用户角色](https://docs.mongodb.com/manual/reference/built-in-roles/)进行设置。它们提供了一个定义好的 API，并支持跨主设备和辅助设备进行扩展。在 3.6 中，MongoDB 支持每个数据承载节点多达 1，000 个并发变更流。

它们是持久的，这意味着它们依赖于在[副本集](https://docs.mongodb.com/manual/replication/index.html)中提交的大部分数据。数据变化的顺序也很重要，对吗？3.6 还引入了一个全局逻辑时钟，除了其他用途之外，它将确保客户机总是能够按照它们在服务器上发生的顺序得到更改。这对`replace`和`update`操作非常重要。

### 总结起来

随着 3.6 更接近生产，文档也将被进一步清理，关于变更流的更多技术细节即将出现。变更流是 3.6 服务器所固有的，在该产品的社区版和企业版中都可用。MongoDB 版本今天可以作为发布候选版本从[下载](https://www.mongodb.com/download-center)。这意味着，虽然这里讨论的变更流的高级概念可能会在最终版本中保持不变，但是可能会有一些微妙的变化。也就是说，变更流是在新版本中实现的一个很棒的特性。如果您的应用程序需要或可以使用实时信息，我鼓励您去看看。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？并得到有益的回应。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F&t=Change%20Streams%2C%20Coming%20Soon%20in%20MongoDB%203.6&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi2.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F10%2Fchange_streams_feature.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Change%20Streams%2C%20Coming%20Soon%20in%20MongoDB%203.6)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F&title=Change%20Streams%2C%20Coming%20Soon%20in%20MongoDB%203.6)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F&title=Change%20Streams%2C%20Coming%20Soon%20in%20MongoDB%203.6)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Change%20Streams%2C%20Coming%20Soon%20in%20MongoDB%203.6&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fchange-streams-coming-soon-mongodb-3-6%2F)

MongoDB 3.6 即将发布的帖子[Change Streams](https://www.kenwalger.com/blog/nosql/mongodb/change-streams-coming-soon-mongodb-3-6/)最先出现在肯·w·阿尔杰的[博客上。](https://www.kenwalger.com/blog)