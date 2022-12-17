# 分布式数据库的 CP

> 原文：<https://dev.to/horia141/cp-for-distributed-databases-5a5>

一些分布式数据库被描述为 [CP](https://en.wikipedia.org/wiki/CAP_theorem) 。 [BigTable](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf) 就是一个[的例子](https://www.slideshare.net/GrishaWeintraub/cap-28353551)，在这方面它与 AP 的[迪纳摩](http://cloudgroup.neu.edu.cn/papers/cloud%20data%20storage/dynamo-sosp-2007.pdf)形成鲜明对比。然而，如果你仔细想想，事情就有点奇怪了。如果您曾经使用过 BigTable 或它的开源克隆 HBase，您就会知道，如果一个节点宕机，它不会停止接受写入。在使用这些工具的规模下，节点一直在下降。那么是不是人物塑造错了呢？还是描述中遗漏了什么？

事实上，上限定理是以非常精确的方式定义的。它给了我们很多关于真实世界的直觉，但它并没有准确地模拟它。它处理一个所谓的*读写寄存器*——一个可以被*读*或*写*的分布式对象。这是可以是 CP、AP 或 CA 的实体。然而，即使像 NoSQL 键值存储这样接口相对简单的数据库也比寄存器复杂得多。对于 BigTable/HBase，单个行(键-值对)可以与寄存器同化。事实上，对行的操作是事务性的，就其行为而言，它是 CP。

从物理上讲，键空间被划分为称为平板的范围。每台平板电脑由平板电脑服务器处理，通常复制三次。根据 CP 的行为，如果平板电脑服务器出现故障或有分区，它处理的平板电脑将无法工作。更准确地说，我们无法成功写入，因为写入需要所有副本接收数据的拷贝。然而，所有其他的平板电脑仍然是好的，系统作为一个整体仍然或多或少处于正常工作状态。

这实际上意味着，例如，如果系统的一小部分用户的数据存储在发生故障的服务器上，他们将会遇到问题。类似地，并非离线分析中的所有数据都可用。这并不是世界末日——就像完全关闭核电站所暗示的那样。就像在单节点数据库中会发生的那样。

类似的一篇更好的文章是 Martin Kleppmann 的[请停止称数据库为 CP 或 AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) 。