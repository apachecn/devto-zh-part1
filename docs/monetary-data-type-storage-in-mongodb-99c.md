# MongoDB 中的货币数据类型存储

> 原文：<https://dev.to/kenwalger/monetary-data-type-storage-in-mongodb-99c>

数据库中经常存储的一条信息是货币数据。有时，这给数据存储带来了挑战，因为我们需要决定存储什么价值。存储数据的一个选择是严格以数值存储数据。如果一件商品的价格是 12.99 美元，我们可以存储 12.99 美元的货币价值，并将其指定为美元。

### 货币价值的烦恼

然而，当使用双精度值时，这通常会导致一些舍入和数据精度问题。例如，如果一个值是 13.4999999999，它可能被存储为 13.500000000。从长远来看，这些舍入问题显然会造成问题。

另一种选择可能是将值存储为字符串，这在保持精度的同时，会给数据计算带来一些挑战。存储货币数据的另一种常用方法是存储商品的美分价值。例如，在美国，我们使用面值为 100 美分的美元。因此，我们可以将一件价值 11.99 美元的商品存储为 1199，然后执行转换计算以返回到 11.99。

### 存储货币值

如上所述，我们可以用多种方式存储数据。然而，一般来说，有两种基本方法。

第一种方法是将显示给客户的值,$11.99 存储为字符串，然后将近似值存储为浮点型。大致如下的东西:

```
{
    product_name: "fidget spinner",
    price: { value: "11.99", float_value: 11.99000000000, currency: "USD" }
} 
```

这当然可行，但仍有可能产生舍入误差，并且有两个不同的值必须更新。如果我们可以简单地将我们的*值*存储在数据库中，这不是很好吗？幸好有。

###### MongoDB 中的 NumberDecimal

[MongoDB](https://www.mongodb.com) 的[版本 3.4](http://www.kenwalger.com/blog/nosql/new-version-new-features/) 中的一个特性是支持 [NumberDecimal](https://docs.mongodb.com/manual/core/shell-types/#shell-type-decimal) 数据类型。此数据类型允许基于 128 位十进制的值。它是专为需要存储货币或高精度值的应用而设计的。它是 BSON 十进制类型的一个实现。由于 MongoDB 以 BSON 格式存储数据，它允许我们轻松地在数据库中建模货币数据。

现在，对于我们的[小动作微调器](https://www.amazon.com/gp/product/B01N7T7FPY/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01N7T7FPY&linkCode=as2&tag=kenwalgersite-20&linkId=3f434a08033985bcdf0b41fc8a19ca92) [![](img/5593c1a9f2849b86e9e3e3d5e06310cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Pm6_A_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB01N7T7FPY) 产品，我们可以使用 NumberDecimal 建模我们的数据并利用它的特性。

```
{
    product_name: "fidget spinner",
    price: { value: NumberDecimal("11.99"), currency: "USD" }
} 
```

这使得我们也不需要比例因子。货币数据将以数学上有用的方式存储在服务器上。这允许使用 MongoDB 的[聚合管道](https://docs.mongodb.com/manual/core/aggregation-pipeline/)在服务器上进行计算。通过在服务器上进行计算，我们获得了更少的网络流量，这最终会带来更好的应用程序。

### 总结起来

当在 MongoDB 中存储货币数据时，我强烈推荐使用 NumberDecimal 作为您的数据类型。升级到 3.4 版本是另一个原因。如果您还没有升级，现在可能是升级的好时机。或者更好的是，查看他们的 DBaaS 产品 [Atlas](http://www.kenwalger.com/blog/nosql/mongodb/mongodb-atlas/) 。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下，你可以说“Alexa，问 MongoDB 什么是 BSON？并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。*

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F&t=Monetary%20Data%20Type%20Storage%20in%20MongoDB&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi2.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F07%2Fmonetary-1-e1500578574705.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Monetary%20Data%20Type%20Storage%20in%20MongoDB)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F&title=Monetary%20Data%20Type%20Storage%20in%20MongoDB)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F&title=Monetary%20Data%20Type%20Storage%20in%20MongoDB)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Monetary%20Data%20Type%20Storage%20in%20MongoDB&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmonetary-data-type-storage-mongodb%2F)

MongoDB 中的货币数据类型存储的帖子[最早出现在肯·w·阿尔杰](https://www.kenwalger.com/blog/nosql/mongodb/monetary-data-type-storage-mongodb/)的[博客上。](https://www.kenwalger.com/blog)