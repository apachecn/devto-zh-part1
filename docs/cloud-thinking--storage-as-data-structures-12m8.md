# 云思维:作为数据结构的存储

> 原文：<https://dev.to/craignicol/cloud-thinking--storage-as-data-structures-12m8>

我们都经历过保存文件对性能的影响。我们使用缓冲区，并且异步使用。

[Azure 存储有 blob](https://docs.microsoft.com/en-us/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)。它们看起来像文件，如果你一次写一行而不是缓冲，它们会像文件一样失败。但是它们不是文件，它们是数据结构，你需要理解它们，就像你在看数组和链表时需要理解 O(N)一样。你在优化插入、追加或读取、[或成本](https://craignicol.wordpress.com/2016/10/18/cloud-thinking-efficiency-as-a-requirement/)吗？

我知道忽视性能和责怪“网络”很有诱惑力，但是[你拥有你的依赖](https://craignicol.wordpress.com/2016/11/04/you-own-your-dependencies/)和[你可以做得更好](https://craignicol.wordpress.com/2016/04/19/speed-peak-performance/)。了解您的工具，并将数据结构作为持久存储。毕竟，在新的无服务器世界里，这是你所拥有的一切。

* * *

[了解 Block Blobs、Append Blobs 和 Page blob | Microsoft Docs](https://docs.microsoft.com/en-us/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)