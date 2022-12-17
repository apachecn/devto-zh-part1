# 在 MongoDB 中设置副本集

> 原文：<https://dev.to/damcosset/setting-up-a-replica-set-in-mongodb>

## 简介

如果您打开您的终端并启动一个 *mongod* 实例，没有任何选项，您使用的是一个*独立服务器*。您的数据有一份副本。这是一种快速简单的入门方式，但在生产环境中却极其危险。

服务器崩溃可能会使您的数据不可用。硬件问题可能会迫使您移动数据。复制会在多台服务器上保留多个完全相同的数据副本。如果您的一台服务器发生问题，您可以用另一台服务器来代替它，保持您的应用程序运行和数据安全。

在 MongoDB 中，复制是通过创建一个*副本集*实现的。一个*副本集*由多个服务器组成。其中之一被定义为*初选*。这是接受客户端请求的服务器。其他服务器被称为*辅助服务器*。他们有一份你的原始数据。您的主服务器关闭，其中一个辅助服务器成为主服务器，您的数据仍然可用。

## 设置副本集

首先，让我们为每个节点创建三个数据目录。打开终端窗口并键入:

`mkdir -p ~/data/rs{1,2,3}`

或者 Windows 上的这个:

`md c:\data\rs1 c:\data\rs2 c:\data\rs3`

接下来，在三个不同的终端窗口中运行以下命令:

```
mongod --replSet introRep --dbpath ~/data/rs1 --port 27017 
mongod --replSet introRep --dbpath ~/data/rs2 --port 27018 
mongod --replSet introRep --dbpath ~/data/rs3 --port 27019 
```

Enter fullscreen mode Exit fullscreen mode

*注:*在 Windows 上用 *c:\data\rs1* 替换 *~/data/rs1* 。另外两个也一样。

让我们分解这些命令:

我们创建了三个不同的 mongod 实例。

*   - replSet

我们对 mongod 实例说，它是副本集的一部分。同一副本集中的每个主机必须具有相同的集名。在这里，我选择了 *introRep*

*   --dbpath

定义这个 mongod 实例存储数据的位置。我们之前创建了这些目录。每个集合被分配一个不同的目录。

*   -港口

不言而喻，这些集不能在同一个端口上运行。27017 是 mongod 的默认端口。另外两个我用 27018 和 27019。

很好，现在你有三个独立的 *mongod* 进程在运行。

## 配置

在这一点上，我们的 mongod 进程彼此并不了解。我们需要用副本集的每个成员的列表创建一个配置，并通知我们的 mongod 进程。

打开一个新的终端窗口，通过指定一个端口连接到一个 mongod 进程:

`mongo --port 27017`

接下来，我们将创建一个配置文档。该文档将有两个密钥:

*   **_id** :副本集的名称。对于我们的例子， *introRep*

*   **成员**:文档数组。每个文件都有一个 *_id* 键和一个*主机*键。它代表了我们副本集中的每一个集合。

下面是它的样子:

```
config = {
... _id: "introRep",
... members: [
... {_id: 0, host: "localhost:27017"},
... {_id: 1, host: "localhost:27018"},
... {_id: 2, host: "localhost:27019"}
... ]}
{ 
```

Enter fullscreen mode Exit fullscreen mode

将它存储在一个变量中，并将其传递给 *rs.initiate()* 方法来初始化一个副本集:

`rs.initiate(config)`

太好了， *localhost:27017* 会通知其他成员新的配置。之后，他们将进行初选。

## 副本集状态

您的副本集现在功能齐全。如果您使用 mongo 和适当的端口标志启动所有三个集合，您将看到一个以*intro rep>PRIMARY>T1】为前缀，另外两个以*intro rep>SECONDARY>T3】为前缀。**

要获得关于您的副本集的更多信息，您可以使用 *rs.status()* 助手:

```
introRep:PRIMARY> rs.status()
{
    "set" : "introRep",
    "date" : ISODate("2017-08-15T15:37:14.734Z"),
    "myState" : 1,
    "term" : NumberLong(1),
    "heartbeatIntervalMillis" : NumberLong(2000),
    "optimes" : {
        "lastCommittedOpTime" : {
            "ts" : Timestamp(1502811433, 1),
            "t" : NumberLong(1)
        },
        "appliedOpTime" : {
            "ts" : Timestamp(1502811433, 1),
            "t" : NumberLong(1)
        },
        "durableOpTime" : {
            "ts" : Timestamp(1502811433, 1),
            "t" : NumberLong(1)
        }
    },
    "members" : [
        {
            "_id" : 0,
            "name" : "localhost:27017",
            "health" : 1,
            "state" : 1,
            "stateStr" : "PRIMARY",
            "uptime" : 949,
            "optime" : {
                "ts" : Timestamp(1502811433, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2017-08-15T15:37:13Z"),
            "electionTime" : Timestamp(1502810832, 1),
            "electionDate" : ISODate("2017-08-15T15:27:12Z"),
            "configVersion" : 1,
            "self" : true
        },
        {
            "_id" : 1,
            "name" : "localhost:27018",
            "health" : 1,
            "state" : 2,
            "stateStr" : "SECONDARY",
            "uptime" : 613,
            "optime" : {
                "ts" : Timestamp(1502811433, 1),
                "t" : NumberLong(1)
            },
            "optimeDurable" : {
                "ts" : Timestamp(1502811433, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2017-08-15T15:37:13Z"),
            "optimeDurableDate" : ISODate("2017-08-15T15:37:13Z"),
            "lastHeartbeat" : ISODate("2017-08-15T15:37:13.925Z"),
            "lastHeartbeatRecv" : ISODate("2017-08-15T15:37:14.472Z"),
            "pingMs" : NumberLong(0),
            "syncingTo" : "localhost:27017",
            "configVersion" : 1
        },
        {
            "_id" : 2,
            "name" : "localhost:27019",
            "health" : 1,
            "state" : 2,
            "stateStr" : "SECONDARY",
            "uptime" : 613,
            "optime" : {
                "ts" : Timestamp(1502811433, 1),
                "t" : NumberLong(1)
            },
            "optimeDurable" : {
                "ts" : Timestamp(1502811433, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2017-08-15T15:37:13Z"),
            "optimeDurableDate" : ISODate("2017-08-15T15:37:13Z"),
            "lastHeartbeat" : ISODate("2017-08-15T15:37:13.925Z"),
            "lastHeartbeatRecv" : ISODate("2017-08-15T15:37:14.559Z"),
            "pingMs" : NumberLong(0),
            "syncingTo" : "localhost:27018",
            "configVersion" : 1
        }
    ],
    "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看成员数组，您会看到我们列出了三个集合。在我的机器上， *localhost:27017* 被选为主服务器。我们可以通过将 *stateStr* 设置为 *PRIMARY* 来看到这一点。另外两个设置为*次级*。

恭喜你！您成功地用 MongoDB 设置了一个副本集！！

来源: [MongoDB:权威指南，第三版](http://shop.oreilly.com/product/0636920049531.do)