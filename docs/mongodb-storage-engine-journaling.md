# MongoDB 存储引擎日志记录

> 原文：<https://dev.to/kenwalger/mongodb-storage-engine-journaling>

前几天我遇到了一个问题，这个问题与在 [MongoDB](https://www.mongodb.com) 中写日志有关。具体来说，它在不同的受支持存储引擎中是如何处理的，是否有必要使用。关于这个话题有一个有趣的讨论，所以我想我会产生一些想法和解释。

首先，一些问题出现了。MongoDB 日志到底是什么？为什么写日记很重要？为了这篇文章的方便，我将把这些信息与 mongod 的 64 位版本相关联，并且基于数据库的 [3.4 版本](https://www.mongodb.com/download-center#community)。

### 什么是日志？

就像人们使用[日志](https://www.amazon.com/gp/product/B00J7SDKSA/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00J7SDKSA&linkCode=as2&tag=kenwalgersite-20&linkId=0626591f4e7de8fcb9928ede75f28ed3) [![](img/c256b4f02f6c1f6664eaae5f6d2bdd47.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B00J7SDKSA) 来记录思想和日常事件一样，MongoDB 使用日志来确保数据的完整性。这是通过将数据*首先*写入日志文件，然后*再*写入核心数据文件来实现的。如果服务器意外关闭，数据可以恢复到一致的状态。

这是通过 MongoDB 的[写操作](https://www.kenwalger.com/blog/nosql/mongodb-crud-with-python/)持久性保证来完成的。如果您的 [mongod](https://www.kenwalger.com/blog/nosql/mongodb-cli-tools/) 进程以一种意外的方式停止，当它重新启动时，来自日志的数据将用于重新应用写操作。当启用日志记录时，MongoDB 为日志数据创建一个名为`journal`的子目录，它位于`[dbPath](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath)`目录下，包含预写日志。

由于 MongoDB 中每个不同的存储引擎实现崩溃弹性和数据持久性的方式*略有不同*，让我们看看日志是如何利用的。

### 存储引擎实施

MongoDB 主要使用三种不同的存储引擎。 [MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/) 、 [WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/) 和[内存](https://docs.mongodb.com/manual/core/inmemory/)。他们各有各的长处和短处。这些差异超出了本文的范围，但是我想看看日志是如何在每一个中实现的。

###### MMAPv1

从 MongoDB 3.2 版本开始，MMAPv1 不再是默认的存储引擎。然而，它仍然在使用，在某些情况下是一个更好的选择。因此，了解日志记录在默认配置下如何与此存储引擎一起工作还是有好处的。

简而言之，当发出写命令时，操作被应用到内部私有视图，然后被写入日志。一旦日志中的数据得到更新，这些更改将应用到内部共享视图，然后写入磁盘。

在 MMAPv1 中，日志在称为组提交的批处理中每 100 毫秒更新一次。但是，在将共享视图刷新到磁盘的过程中，每 60 秒就会将数据写入磁盘。根据系统内存的数量和可用性，数据刷新可能会更频繁。

那么日记的重要性在哪里呢？在 mongod 进程意外关闭的情况下，日志可以用来恢复数据。如果没有独立服务器上的日志记录，会有一个更长更复杂的修复过程。

在使用正确配置的[副本集](https://docs.mongodb.com/manual/replication/)的系统上，数据恢复*可以*简化，而无需使用修复过程的日志。然而，它仍然没有启用日志功能时干净。

###### 有线电视

WiredTiger 存储引擎采用不同的方法来实现写操作数据并发。WiredTiger 将检查点与日志结合使用。这些[检查点](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger-checkpoints)允许在最后一个检查点之后恢复数据。

当调用写操作时，会拍摄数据的快照。当数据被写入磁盘时(默认情况下每 60 秒一次)，数据将被写入所有数据文件并变得持久。这将成为一个新的检查点，并可用作恢复点。

这允许在没有日志的情况下从最后一个检查点覆盖 WiredTiger。相当狡猾。但是，如果在检查点之间发生意外关闭并且日志记录被禁用，数据将会丢失。因此，WiredTiger 中的日志在检查点之间利用类似于 MMAPv1 的预写日志来保证数据的持久性。

所以在使用 WiredTiger 时，日志和副本集仍然是服务器环境的重要组成部分。它的实现方式与 MMAPv1 略有不同。

###### 内存中

对于那些运行 MongoDB 企业版的用户，有一个存储引擎将数据存储在内存中。因为内存存储在内存中，所以数据是非持久的。日记账的概念不适用于这种情况。

### 学习问题

我在各种学习指南中看到过类似“为什么 WiredTiger 不需要期刊”的问题。正如我们所知，数据一致性并不需要**。至少不会像 MMAP 那样。话虽如此，我可能认为“不必要”这个词有点误导。WiredTiger 的数据一致性模型与 MMAP 不同。日志也许不是“必要的”,但是我不会运行一个没有它的系统。**

 **### 包裹

日志记录的所有这些细节可能需要考虑和管理很多。这是 [MongoDB Atlas](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-atlas/) 的最大优势之一，因为这些内部事务由他们处理。如果您正在运行和/或管理一个 MongoDB 服务器，为了数据完整性，保持日志记录是一个最佳实践。此外，建议您的系统至少使用一个副本集，因为数据恢复通常会更加简单。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下，你可以说“Alexa，向 MongoDB 询问日志的定义？”并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](https://www.kenwalger.com/blog)上查看原始帖子。***