# 星期五爆炸#13

> 原文：<https://dev.to/horia141/friday-blast-13-4a5p>

这是第 13 个星期五爆炸贴。巧合的是[1]今天也是 13 号星期五。

[Fature Toggles(2016)](https://martinfowler.com/articles/feature-toggles.html)-feature Toggles 是一组模式，允许尚未准备好的代码存在于应用程序中，而没有任何影响。这篇文章详细描述了用例、切换类型、基础设施支持等。对他们来说。

[如何安全存储密码(2010)](https://getpocket.com/redirect?url=https%3A%2F%2Fcodahale.com%2Fhow-to-safely-store-a-password%2F&formCheck=c6f4865ffdcf1e165221e221555f47b3)——使用*密码哈希*，如`bcrypt`、`scrypt`等。正如本系列前面的链接所强调的，还有更多，但是我认为快速提醒总是受欢迎的。

[分布式编程的二分搜索法(2015)](http://antirez.com/news/102)——正在生成一个严格递增的整数/id 序列。总的来说，这很难做到。这告诉您许多关于分布式系统的事情——在一台机器上用一个计数器和一些锁就可以实现的事情在分布式环境中变得非常困难。作者也是 Redis 的作者，所以他略知一二。但是提出的算法似乎是坏的或者至少提供了一些不好的保证。无论如何，好好读一读关于设计这些东西以及什么会出错。

[发挥到极致:构建可靠系统的考虑因素(2016)](http://bravenewgeek.com/take-it-to-the-limit-considerations-for-building-reliable-systems/) -主要是关于构建分布式系统的建议。这一次，与“限制”和强制执行有关。基本上，无论何时何地，系统中有一些量可能会无限增长，你应该对它有严格的限制。请求大小、队列深度、要处理的项目、父实体的子实体、响应大小等。都应该受到限制。这可以防止恶意代理试图拒绝系统，也可以防止您自己的代理做同样的事情。

[更好排版的五分钟指南(2017)](http://pierrickcalvez.com/journal/a-five-minutes-guide-to-better-typography) -一篇关于图形和排版设计的文章。

[聚集索引 see 和非聚集索引 seek 之间的差异(2016)](https://dba.stackexchange.com/questions/137724/difference-between-clustered-index-seek-and-non-clustered-index-seek/137731)-stack overflow 问题。似乎有一种误解，认为非聚集索引比聚集索引慢，因为必须先进行搜索/扫描，然后再搜索数据本身。但是根据数据的大小和非聚集索引的紧凑程度，情况可能并非如此。因为第一次查找/扫描可能完全发生在存储器中，或者至少比聚集的页面接触更少的页面。同上，对于计数查询，非聚集索引可能快得多。

[数学地图#视频(2017)](https://www.youtube.com/watch?v=OmJ-4B-mS-Y) ，[计算机科学地图#视频(2017)](https://www.youtube.com/watch?v=SzJ46YA_RaA) ，[物理地图#视频(2017)](https://www.youtube.com/watch?v=ZihywtixUYo) ，[化学地图#视频(2017)](https://www.youtube.com/watch?v=P3RXtoYCW4M) -一组非常精彩的视频，展示了数学、计算机科学、物理和化学的“分支”。这是一个真正的大图景，正如作者自己所指出的，所有的东西或多或少在领域内和领域间都是相互联系的，但看到所有的知识片段都是不同的，足以被称为某种东西，这仍然是很好的。动画也很棒。

* * *

[1]或者不是:o