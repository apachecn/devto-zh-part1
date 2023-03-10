# 升级到 MongoDB 3.4 的三大理由

> 原文：<https://dev.to/weiiishannn/top-3-reasons-to-upgrade-to-mongodb-34>

MongoDB 3.4 于 2016 年 11 月 29 日发布，带来了大量的新特性，在这篇文章中，我将列出升级到 MongoDB 3.4 的三大理由。

## [更快的平衡](https://docs.mongodb.com/manual/release-notes/3.4/#faster-balancing)

在以前的版本中，平衡器移动或预分割数据块需要很长时间(每个数据块 4 秒，我上次是在 3.2.10 中看到的)。想象一下，如果你有 100，000 块要预分割，这将需要几天时间。这有两个原因:

1.  在分片集群中，任何时间点都只能进行一次区块迁移。这是一个单线程操作
2.  [这个](https://jira.mongodb.org/browse/SERVER-26778)。

在 MongoDB 3.4 中，对于具有 n 个分片的分片集群，MongoDB 最多可以执行 n/2(向下舍入)个并发块迁移。因此，如果您有一个 4 shard MongoDB 集群，它将一次迁移 2 个块！如果你的碎片少于 4 个，不幸的是，行为将保持不变。:(

## [可线性化读取关注点](https://docs.mongodb.com/manual/release-notes/3.4/#linearizable-read-concern)

一般来说，MongoDB 有各种 [readConcern 选项](https://docs.mongodb.com/manual/reference/read-concern/#read-concern-levels)。这些选项允许开发者选择适合他们应用需求的不同 readConcern。如果不重要的查询读取可以回滚的数据，请使用 readConcern:local。使用 readConcern:多数防止读取可以回滚的数据。但是，这并不妨碍您在独特的网络分区边缘情况下读取陈旧数据。

在 MongoDB 3.4 中，使用 readConcern:线性化也可以防止过时读取。此功能不是免费的，并且具有最高的性能开销。readConcern:local 仅需要 1 次操作来返回结果。readConcern:majority 需要来自副本集中大多数节点的多数确认，这取决于您的网络延迟。read concern:linear able 需要对副本集进行额外的无操作多数写入，以确保正在读取的数据是持久的而不是陈旧的。

本质上，readConcern:线性化做的是后面的[:](https://jira.mongodb.org/browse/SERVER-24497)

1.  客户端向主节点发送查询
2.  节点获得没有错误的结果
3.  节点向群集中的其他节点发送 noop 多数写入，以确认它仍然是主节点。
4.  结果返回给客户端

## [网络压缩](https://docs.mongodb.com/manual/reference/configuration-options/#net-compression-option)

我不知道为什么这没有作为一个新特性被广泛宣传。我非常喜欢它，因为现在 mongos/mongod 之间的所有流量都被压缩了，所以我们节省了带宽。如果您在 AWS 上运行它，或者如果您受到网络带宽的限制，这确实会为您节省很多钱。

## 总结

总的来说，我同意 infoworld 的观点，MongoDB 3.4 是一个显而易见的升级！我们目前正在等待它在我们选择的操作系统上可用，这样我们就可以开始推出它了！

希望这有所帮助！:)

魏善传旨