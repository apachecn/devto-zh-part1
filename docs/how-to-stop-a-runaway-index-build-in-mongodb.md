# 如何在 MongoDB 中停止失控的索引构建

> 原文：<https://dev.to/scalegrid/how-to-stop-a-runaway-index-build-in-mongodb>

[![How to Stop a Runaway Index Build in MongoDB - ScaleGrid Blog](img/f04b103a1233ab22ba5f4833b416328f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9dH3aveP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3848622/How-to-stop-a-runaway-index-build-in-MongoDB---ScaleGrid-Blog.jpg)

MongoDB 中的索引构建会对 MongoDB 集群的可用性产生负面影响。如果在生产服务器上的大型集合上触发前台索引构建，您可能会发现在索引构建完成之前，您的集群没有响应。对于大型集合，这可能需要几个小时或几天，如[MongoDB 中索引构建的风险](https://scalegrid.io/blog/the-perils-of-building-indexes-on-mongodb/ "The perils of index building in MongoDB - ScaleGrid Blog")中所述。

[推荐的](https://docs.mongodb.com/manual/core/index-creation/ "MongoDB Index Build Recommendation - ScaleGrid Blog")最佳实践是在后台触发索引构建，然而，在大型集合索引上，我们已经看到这种方法的多个问题。·在三节点集群的情况下，两个辅助节点都开始构建索引，并停止响应任何请求。因此，主节点没有仲裁，并进入辅助节点状态，从而关闭集群。此外，从命令行触发的默认索引构建是前台索引构建——这是一个现在普遍存在的问题。在未来的版本中，我们希望这成为默认的背景。

一旦你触发了一个索引，简单地重启服务器并不能解决我们的问题；MongoDB 将从停止的地方开始构建索引。如果您之前在重启后运行后台索引构建，现在它变成了前台索引构建，所以在这种情况下，重启可能会使问题变得更糟。

如果您已经触发了索引构建，如何停止它呢？幸运的是，停止索引构建相对容易。

## 选项 1:终止索引构建过程

使用 db.currentOp()定位索引构建过程，然后使用 db.killOp( <opid>)终止该操作。索引操作看起来会像这样:</opid>

| [code language = " JavaScript "]
{
" opid ":820659355，
"active" : true，
"lockType" : "write "，
....
"op" : "insert "，
"ns" : "xxxx "，
"query" : {
}，
"client" : "xxxx "，
"desc" : "conn "，
" msg ":" index:(2/3)btree bottom up 292168587/398486401 64% "
}
[/code]
 |

如果正在构建索引的节点不响应新连接，或者 killOp 不起作用，请使用下面的选项 2:

## 选项 2:配置“noIndexBuildRetry”并重新启动

MongoDB 提供了一个[-noIndexBuildRetry "](https://docs.mongodb.com/v3.2/reference/program/mongod/ "noIndexBuildRetry - ScaleGrid Blog")选项，该选项指示 MongoDB 在重启时停止构建不完整的索引。

这个参数似乎不被配置文件支持，只是作为 mongod 进程的一个参数。我们不喜欢使用这个选项手动运行 mongod，因为如果您意外地以提升的用户(例如 root)身份运行 mongod 进程，它最终会更改所有文件的权限。此外，一旦以“root”身份运行，我们再次以 mongod 身份运行该进程时会出现间歇性问题。

一个更简单的选择是编辑/etc/init.d/mongod 文件。寻找这一行:

| [code language = " JavaScript "]
OPTIONS = "-f $ config file "
[/code]
 |

替换为此行:

| [code language = " JavaScript "]
OPTIONS = "-f＄config file-noIndexBuildRetry "
[/code]
 |

## 详细步骤

出于讨论的目的，我们提供 CentOS/RedHat/Amazon Linux 的说明。

1.  ### 配置"- noIndexBuildRetry "

    如上所述，将"- noIndexBuildRetry "选项添加到所有数据节点中。
2.  ### 重新启动构建索引的所有节点

    查看每个数据服务器的 mongod 日志文件，确定它是否正在构建索引。如果是，重启服务器“服务 mongod 重启”。
3.  ### 删除不完整的索引

    一旦所有相关节点重新启动，查看索引列表，如果在列表中看到不完整的索引，则删除它。
4.  ### 删除"- noIndexBuildRetry "

    编辑/etc/init.d/mongod 文件，删除您在步骤 1 中添加的- noIndexBuildRetry 选项，这样我们就可以恢复到恢复索引构建的默认行为。

如有任何其他问题，请通过 [support@scalegrid.io](mailto:support@scalegrid.io) 联系我们。

索引快乐！