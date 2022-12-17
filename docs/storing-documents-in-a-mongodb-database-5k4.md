# 在 MongoDB 数据库中存储文档？

> 原文：<https://dev.to/kenwalger/storing-documents-in-a-mongodb-database-5k4>

[MongoDB](https://www.mongodb.com) 被认为是一个文档数据库。正如在技术世界中经常发生的那样，同一个词被用于不同的概念。在这种情况下,“文档”这个词有时会引起混淆。存储文档是数据库的工作吗？

对于我们许多人来说，当我们听到“文档”这个词时，我们会想到一个[微软 Word ](https://www.amazon.com/gp/product/B0153V62P2/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B0153V62P2&linkCode=as2&tag=kenwalgersite-20&linkId=76afa21bc5320f49aaf6bb8b88f9ecc7)[![](img/78f84371a272dcd02c1e42d18cdb1d7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--endpTOkL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB0153V62P2) 文档。有些人可能会想到 Adobe Acrobat 的便携式文档格式文件。或任何其他常见的“文档概念”。在 MongoDB 中，事实上在许多其他数据库模型中，术语[文档](https://docs.mongodb.com/manual/core/document/)有不同的含义。我写了一篇关于[文档模型](https://www.kenwalger.com/blog/nosql/document-model/)的文章，因为它特别适合 MongoDB，并建议看看更多信息。然而，简而言之，在 MongoDB 中，文档是信息的单独记录。

如果我们真的想将“文档”存储在数据库中呢？有没有用 MongoDB 存储文档的最佳实践？有哪些选择可以考虑？

### MongoDB GridFS

GridFS 允许在一个 MongoDB 文档中存储比 T2 BSON 规范 T3 所允许的 16MB 更多的数据。这允许将数据库记录分割到磁盘上的独立文件中。除了 GridFS 文档中列出的好处之外，这还可以带来一些非常具体的好处。例如，使用[复制](https://docs.mongodb.com/manual/replication/)，可以将文件部署在多个设备上，并利用 MongoDB 的同步特性。然而，许多文件系统本身也说明了这一点。

用 GridFS 存储文档本身就是一个主题，超出了本文的范围。不过，这绝对是要报道的事情之一。

### 存储文件的另一个选项

这里先说一下，我在这方面有点偏颇。我从来不热衷于存储“文档”、图像、视频等。*内部*一个数据库。我一直倾向于将文件存储在文件服务器、[网络附加存储](https://www.amazon.com/gp/product/B016EWTC7E/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B016EWTC7E&linkCode=as2&tag=kenwalgersite-20&linkId=76f40e61d4b2abb9bbe151589ed3030f) [![](img/a786c6efccc0068f87e180dad9a3a48c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fI4aCimt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB016EWTC7E) 系统上，或者像[亚马逊 S3 ](https://www.amazon.com/gp/product/B00YSILDE2/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00YSILDE2&linkCode=as2&tag=kenwalgersite-20&linkId=3e14030b7296b82d2c7079c397a9fdb8) [![](img/f0a025dfe388c08c9a69f802760fa68e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CQNbN6D---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3DB00YSILDE2) 这样的云中某个地方。

那么如何将这两个概念结合起来呢？将文件存储在数据库之外的磁盘上，但同时利用数据库的能力？嗯，您使用 MongoDB 文档模型和灵活的模式来处理文件的元数据，以及指向文件位置的链接。大致如下的东西:

```
{
    _id : ,
    file_title : "",
    file_URL : "<S3_URL>",
    uploaded_by : "<user_name>",
    created_at : <Timestamp>,
    updated_at : <Timestamp>,
    updated_by : "<user>",
    department : "<department_name>",
    tags : [
        { name : "<tag1>" },
        { name : "<tag2>" }
    ],
    version : <Timestamp>
} 
```

这种设计考虑到了一些良好的性能和查询可能性。例如，如果我们想要搜索“在 2016 年 1 月 1 日和 2017 年 10 月 1 日之间由 Ken 上传的标记有 Python 主题的所有文档”,我们可以进行查询。我们还可以利用文件系统本身，它通常针对文件存储读写进行了更好的优化。

### 总结起来

在这篇短文中，我希望已经阐明了存储在 MongoDB 数据库中的文档的概念，比如 Word 或 PDF 文件。相对于文档的概念，即*是*一个 MongoDB 数据库记录。虽然 MongoDB 可以用于文件存储，但在我看来，文件存储应该留给文件存储系统。

设计数据库模式来维护文档的元数据和链接，并利用 MongoDB 丰富的查询语言来查找信息。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？并得到有益的回应。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F&t=Storing%20Documents%20in%20a%20MongoDB%20Database%3F&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi1.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F10%2Fwp_feature_store_documents-e1506995740114.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Storing%20Documents%20in%20a%20MongoDB%20Database%3F)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F&title=Storing%20Documents%20in%20a%20MongoDB%20Database%3F)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F&title=Storing%20Documents%20in%20a%20MongoDB%20Database%3F)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Storing%20Documents%20in%20a%20MongoDB%20Database%3F&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Funcategorized%2Fstoring-documents-mongodb-database%2F)

在 MongoDB 数据库中存储文档？最早出现在肯·w·阿尔杰的[博客上。](https://www.kenwalger.com/blog)