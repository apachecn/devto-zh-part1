# MongoDB:复制在起作用

> 原文：<https://dev.to/damcosset/mongodb-replication-in-action>

本文从我在 MongoDB 中设置副本集[的地方开始。如果你想继续下去，请务必阅读这篇文章。](https://dev.to/damcosset/setting-up-a-replica-set-in-mongodb)

## 简介

我们已经配置了副本集。我们让每个节点运行以下命令:

```
mongod --replSet introRep --dbpath ~/data/rs1 --port 27017 
mongod --replSet introRep --dbpath ~/data/rs2 --port 27018 
mongod --replSet introRep --dbpath ~/data/rs3 --port 27019 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`mongo --port portNumber`连接到每个实例

现在，您需要连接到您的主节点。如果您已经阅读了上一篇文章，您应该会在 shell 的每一行的开头看到一个 *introRep:PRIMARY >* 。在我的例子中，主服务器在端口 27017 上，但对您来说可能是不同的端口。

## 四处游玩

让我们写一些文件给我们的主。我们将编写 10000 个简单的文档，像这样:

```
introRep:PRIMARY> use test switched to db test introRep:PRIMARY>  for( i = 0; i < 10000; i++){ db.example.insertOne({count: i})}
{
    "acknowledged" : true,
    "insertedId" : ObjectId("599994478d005c672de000ed")
}
introRep:PRIMARY> db.example.count()
10000 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们现在有了主要的人口。复制的概念意味着您的数据在辅助节点上被完全相同地复制。让我们连接到我们的一个辅助设备。首先，让我们运行 *isMaster()* 。这个命令充当了一个浓缩的 *rs.status()* 。你会看到一些信息，比如哪个节点是你的主节点，你连接到哪个节点:

```
introRep:PRIMARY> db.isMaster()
{
    "hosts" : [
        "localhost:27017",
        "localhost:27018",
        "localhost:27019"
    ],
    "setName" : "introRep",
    "setVersion" : 1,
    "ismaster" : true,
    "secondary" : false,
    "primary" : "localhost:27017",
    "me" : "localhost:27017",
    "electionId" : ObjectId("7fffffff0000000000000002"),
    "lastWrite" : {
        "opTime" : {
            "ts" : Timestamp(1503237420, 1),
            "t" : NumberLong(2)
        },
        "lastWriteDate" : ISODate("2017-08-20T13:57:00Z")
    },
    "maxBsonObjectSize" : 16777216,
    "maxMessageSizeBytes" : 48000000,
    "maxWriteBatchSize" : 1000,
    "localTime" : ISODate("2017-08-20T13:57:08.312Z"),
    "maxWireVersion" : 5,
    "minWireVersion" : 0,
    "readOnly" : false,
    "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道我不是次要的，我知道我在哪里。让我们在 *localhost:27018* :
上创建一个连接

```
introRep:PRIMARY> secondary = new Mongo("localhost:27018")
connection to localhost:27018

introRep:PRIMARY> secondaryDB = secondary.getDB("test")
test introRep:PRIMARY> secondaryDB.example.find()
Error: error: {
    "ok" : 0,
    "errmsg" : "not master and slaveOk=false",
    "code" : 13435,
    "codeName" : "NotMasterNoSlaveOk"
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 *Mongo()* 构造函数来实例化一个连接。然后，我们将我们的数据库 *test* 存储在一个变量中来查询它。但是，如你所见，我们以错误*“not master and slave ok = false”*结束。

这是为什么呢？嗯，当您查询时，辅助节点可能没有最新的数据。因此，默认情况下，读取请求将被拒绝。如果您意外地连接到您的一个辅助节点，这将保护您的应用程序不会有过时的数据。

要允许在辅助服务器上进行查询，我们必须告诉 Mongo 我们可以从辅助服务器上读取数据，就像这样:

`introRep:PRIMARY> secondary.setSlaveOk()`

*小心:*方法用在连接上，而不是数据库上。我们再来试着查询一下:

```
introRep:PRIMARY> secondaryDB.example.find()
{ "_id" : ObjectId("5999943d8d005c672ddfd9de"), "count" : 0 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9df"), "count" : 1 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9ec"), "count" : 14 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9eb"), "count" : 13 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9e1"), "count" : 3 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9f1"), "count" : 19 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9e4"), "count" : 6 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9e0"), "count" : 2 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9e9"), "count" : 11 }
{ "_id" : ObjectId("5999943d8d005c672ddfd9ea"), "count" : 12 }

... 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们试着给我们的中学写信:

```
introRep:PRIMARY> secondaryDB.example.insert({"count": 10001})
WriteResult({ "writeError" : { "code" : 10107, "errmsg" : "not master" } })
introRep:PRIMARY> secondaryDB.example.count()
10000 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个错误，写入失败。辅助节点仅接受通过复制获得的写入。

## 新建主

关于复制的最后一个有趣的事情。如果您的主服务器关闭，辅助服务器将被选为新的主服务器。让我们通过停止我们的主要活动来看看它的实际效果:

`db.adminCommand({"shutdown": 1})`

您将看到一些错误消息，因为我们连接到的实例失去了连接。不过不用担心，壳不会崩的。现在，在辅助服务器上运行我们之前使用的 *isMaster()* 方法:

```
>secondaryDB.isMaster()
{
    "hosts" : [
        "localhost:27017",
        "localhost:27018",
        "localhost:27019"
    ],
    "setName" : "introRep",
    "setVersion" : 1,
    "ismaster" : false,
    "secondary" : true,
    "primary" : "localhost:27019",
    "me" : "localhost:27018",
    "lastWrite" : {
        "opTime" : {
            "ts" : Timestamp(1503238614, 1),
            "t" : NumberLong(3)
        },
        "lastWriteDate" : ISODate("2017-08-20T14:16:54Z")
    },
    "maxBsonObjectSize" : 16777216,
    "maxMessageSizeBytes" : 48000000,
    "maxWriteBatchSize" : 1000,
    "localTime" : ISODate("2017-08-20T14:16:54.375Z"),
    "maxWireVersion" : 5,
    "minWireVersion" : 0,
    "readOnly" : false,
    "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

看那个！我们的主服务器不再是 localhost:27017，而是 localhost:27019。现在，我可以直接给新的初选写信了。

## 结论

关于复制，需要记住以下几点:

*   客户端(例如您的应用程序)可以向主节点发送与您在独立服务器上发送的操作相同的操作。您可以写、读、构建索引...

*   默认情况下，您不能从辅助节点读取数据。这是安全检查。MongoDB 不能保证您的辅助服务器上的数据将与主服务器上的数据保持一致。如果您不关心您的数据是否是最新的，您可以使用 *setSlaveOk()* 从辅助节点读取数据。

*   客户端无法写入辅助节点。辅助节点仅接受直写复制。

来源: [MongoDB:权威指南，第三版](http://shop.oreilly.com/product/0636920049531.do)