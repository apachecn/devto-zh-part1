# MongoDB 中可重试的写入

> 原文：<https://dev.to/kenwalger/retryable-writes-in-mongodb-27l>

MongoDB 3.4 提供了一些很棒的新特性。MongoDB 版本很快就要发布了，并且引入了许多新特性。正如我之前讨论的，一个特性是[变更流](https://dev.to/kenwalger/change-streams-coming-soon-in-mongodb-36-967)。另一个非常令人兴奋且备受期待的新特性是可重试写入。

### 需要可重试写入

在以前版本的 [MongoDB](https://www.mongodb.com) 中，开发人员和应用程序有责任处理没有发生写入的情况。想象一下，向数据库发送一个创建或更新命令，然后发生了一些事情。发生网络分区，一个[主节点下台](https://docs.mongodb.com/manual/core/replica-set-elections/)，或者一只[蝴蝶](https://www.amazon.com/gp/product/078948983X/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=078948983X&linkCode=as2&tag=kenwalgersite-20&linkId=8afe7c700070a08f2f0053fef2c8416e) [![](img/4de99e29dc606f2900c981c7bd46c93f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xs_WLiN4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D078948983X) 在[厄瓜多尔](https://www.amazon.com/gp/product/0241245745/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0241245745&linkCode=as2&tag=kenwalgersite-20&linkId=61f4992dae867976e401b1909bcc3d30) [![](img/742bc70df9e6a0ccce6246accfa84102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WymV0vpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D0241245745) 扇动翅膀，导致[芝加哥](https://www.amazon.com/gp/product/B01D6P25O4/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01D6P25O4&linkCode=as2&tag=kenwalgersite-20&linkId=4a94952d1dc3aa13dff59353966228ae) [![](img/72db39e4c35a928dfeba44d1a5af50d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---oqMpvWN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB01D6P25O4) 网络故障。或者任何其他的可能性。

### 司机处理

3.6 版中的可重试写入将这些系统故障的处理从应用程序转移到了数据库本身。MongoDB 驱动程序现在能够自动重试许多写操作。同时，服务器负责处理写请求和一次性概念。

###### 支持可重试的写操作

MongoDB 3.6 支持单语句写操作的这种功能，例如:

*   `insertOne()`
*   `updateOne()`
*   `replaceOne()`
*   `deleteOne()`
*   `findOneAndDelete()`
*   `findOneAndReplace()`
*   `findOneAndUpdate()`

支持的多语句操作有`insertMany()`和`bulkWrite()`。尽管在新特性中使用`bulkWrite()`有一些限制。

###### 可重试写入限制

在 MongoDB 3.6 中，不支持可重试行为的现有限制很少。虽然这些限制将来可能会被取消。

1.  具有未确认写问题的写，`{w: 0}`
2.  使用单个语句编写可能影响多个文档的命令
3.  除 insert、delete、update 和 findAndModify 之外的命令。例如，使用`$out`运算符的聚合命令。

### 简单地说它是如何工作的

正如我提到的，可重试的写操作是在服务器上处理的。但是它是通过 MongoDB 3.6 中的另一个新特性，逻辑会话，通过驱动程序实现的。通过创建与服务器的会话，我们现在能够为每个写操作建立唯一的事务标识符。

```
> var session = startSession( { retry_writes : True });
> session.db.collection.updateOne({'_id': 1}, 
     {'$inc': {'counter': 5}}
  ); 
```

该会话的事务标识符由驱动程序重新发送到服务器，以确定*之前的*写尝试是否成功。这个实现带来了一些重大的胜利。

*   应用程序不需要额外的代码，例如保存点或重试逻辑。
*   可重试写入不仅限于幂等运算。
*   对于由于超时异常而无法确认写入成功的操作，它们是安全的。

### 总结起来

可重试写是 MongoDB 团队带来的另一个伟大特性。对于不能容忍任何写入操作丢失的应用程序，可重试写入是一大优势。看 MongoDB 最新发布版的众多原因之一。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？”并得到有益的回应。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F&t=Retryable%20Writes%20in%20MongoDB&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi1.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F11%2Frw_feature.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Retryable%20Writes%20in%20MongoDB)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F&title=Retryable%20Writes%20in%20MongoDB)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F&title=Retryable%20Writes%20in%20MongoDB)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Retryable%20Writes%20in%20MongoDB&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fretryable-writes%2F)

MongoDB 中的帖子 [Retryable Writes 最先出现在肯·w·阿尔杰](https://www.kenwalger.com/blog/nosql/mongodb/retryable-writes/)的[博客上。](https://www.kenwalger.com/blog)