# 周五爆炸#19

> 原文：<https://dev.to/horia141/friday-blast-19-1ana>

[选择简史(2)(2016)](https://idea.popcount.org/2016-11-01-a-brief-history-of-select2/)——涵盖了`select`系统调用的历史。早期的 Unixes 非常简单。

[Select 从根本上被打破(2017)](https://idea.popcount.org/2017-01-06-select-is-fundamentally-broken/)——《Select 简史(2)》的后续，这涵盖了为什么`select`在性能方面没有那么好，以及`poll`和`epoll`如何试图修复它的一些问题。

[竞争条件 vs 数据竞争(2011)](https://blog.regehr.org/archives/490)——关于并发程序中可能出错的事情这个话题。竞争条件是在应用程序中的特定时间发生的任何错误，而数据竞争是两次写入在没有任何同步的情况下写入几个共享内存位置的明显情况。它们是截然不同的，一个可以有一个而没有另一个。有趣的是，数据竞争可以被自动检测到，没有数据竞争的程序实际上有一些很好的特性。

【程序员相信的关于视频的东西的谎言】([https://haasn . XYZ/posts/2016-12-25-false-programmers-believe-about-% 5b video-stuff % 5d . html](https://haasn.xyz/posts/2016-12-25-falsehoods-programmers-believe-about-%5Bvideo-stuff%5D.html))——这些“程序员相信的关于 X 的谎言”有一个山寨产业，这篇文章有“X =视频的东西”。事实上，有很多这样的事情，其中一些非常可怕。如果你曾经不得不与视频互动，值得一读。

sysadmin:你的负载平衡有问题吗？(2016)——负载均衡有两种用途——提供额外的容量或者提供弹性。你的团队需要清楚你是如何运用你的能力的。例如，如果您使用它们来获得额外的容量，您可能会期望碎片几乎达到最大容量。当一个中断时，其他的可能无法处理额外的流量。如果你认为你这样做是为了利益，那你将会过得很糟糕。

[为什么应用程序下载不在 HTTPS 进行？(2012)](https://security.stackexchange.com/questions/18853/why-arent-application-downloads-routinely-done-over-https)——底线是，你基本上只需要真实性，而不是防止 MITMs 或 HTTPS 提供的任何其他东西。此外，由于下载的方式(镜像等)，你需要一些额外的基础设施。

[利特尔法则(2017) #own](http://horia141.com/littles-law.html) -这是我上周自己的一张。这是关于排队论的一个很好的结果，在构建分布式应用中有很多应用。