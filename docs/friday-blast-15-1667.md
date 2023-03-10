# 星期五爆炸#15

> 原文：<https://dev.to/horia141/friday-blast-15-1667>

[大型复杂数据集分析的实用建议(2016)](http://www.unofficialgoogledatascience.com/2016/10/practical-advice-for-analysis-of-large.html?m=1) -来自从事广告工作的谷歌数据科学家的建议。现在，我认为这对于任何从事复杂项目的软件工程师来说都是很好的建议。许多项目将涉及大型数据集的分析、运行实验和 A/B 测试等。

程序员相信的网络谎言(2012) -这些“谎言”文章有一个小小的山寨历史。这一次，网络是目标。

[关于无锁并发，每个系统程序员都应该知道的事情(2017)](https://assets.bitbashing.io/papers/lockless.pdf)——这篇文章的最大部分是关于现代 CPU 提出的内存模型以及如何将其转化为编程语言。反过来，这允许通过使用许多用于实际实现锁/信号量等的工具来实现无锁算法或数据结构。因此，你可以自己使用一些魔法，而不是依赖操作系统提供的 API，比如`pthreads`。让我感兴趣的是，事情和分布式系统有多少相似之处。事实上，很多关于一致性模型等的讨论。源自于此。这是同步系统的一个很好的例子。

[在 Postgres (2017)](https://brandur.org/idempotency-keys) 中实现条状幂等键——处理非事务性系统很难，因为开发人员负责“事务”。作者在这里给出了一个相当复杂的例子，有多个 DB 写入和对外部系统的调用。但这是任何媒体 webapp 在某个时候都会遇到的问题，所以拥有一些好的模式和工具来处理这些情况是至关重要的。

[Linux 上的异步 IO:select、poll 和 epoll(2017)](https://jvns.ca/blog/2017/06/03/async-io-on-linux--select--poll--and-epoll/)——在 Nginx 等现代网络服务器和 Node、Golang one 等基于事件的运行时的核心，我们可以找到一个“事件”系统。这是一种在单线程上高效执行计算的方法。许多所述事件与 IO 相关，并且`select`、`poll`和`epoll`系统调用是被通知 IO 事件的标准接口。

[外部存储器的数据结构(2016)](http://blog.omega-prime.co.uk/2016/07/05/datastructures-for-external-memory/) -简单介绍一下 B 树和 LSM 树，以及关系数据库和 NoSQL 数据库的存储引擎如何使用它们。非常有趣的是，B 树的各种变体本质上是所谓的 B-\ε树的特化。

程序员的用户界面设计(2001 年)——Joel Spolsky 的一系列博客文章被编入一本小电子书。涵盖了一些核心的用户界面设计原则——应该永远放在第一位的东西。这是一篇较长的阅读，但是对于任何软件有用户的人来说都是值得的。

[Apache Spark @scale:一个 60TB 以上的生产用例(2016)](https://code.facebook.com/posts/1671373793181703/apache-spark-scale-a-60-tb-production-use-case/) -脸书希望将他们的一些 ML 管道更新到 Spark，这是对这一过程的回忆。这似乎是一项巨大的努力。无论是公司内部的工程智慧，还是对 Spark 的贡献。如果说有什么不同的话，那就是 Spark 项目从为了让它大规模运行所需要的改变中受益匪浅。“我们所有人”也从中受益。