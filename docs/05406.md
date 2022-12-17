# 星期五爆炸#6

> 原文：<https://dev.to/horia141/friday-blast-6-1373>

本周-诺伯特·鲍姆对 P \neq NP 的证据主张。

[诺伯特·鲍姆关于 P 对 n P (2017)](https://johncarlosbaez.wordpress.com/2017/08/15/norbert-blum-on-p-versus-np/) 和[关于诺伯特·鲍姆声称的 P 不等于 NP 的证明(2017)](https://lucatrevisan.wordpress.com/2017/08/15/on-norbert-blums-claimed-proof-that-p-does-not-equal-np/) -如果你已经猜到人们对证明持怀疑态度，漏洞已经开始出现，那就没有饼干了。这种讨论远远超出了我的能力范围，但我确实设法了解了一点 TCS 中的[电路和电路复杂性](https://en.wikipedia.org/wiki/Circuit_complexity)，更有趣的是，还有非单调电路。这些电路没有非门，因此通过在输入端增加更多的 1，我们只能增加或保留输出端 1 的数量。

[建立账户系统(2017)](https://blog.plan99.net/building-account-systems-f790bf5fdbe0) -对建立账户系统所涉及的所有复杂性提出了一些好的观点。这不仅仅是正确地散列密码，还有大量的功能需要考虑，与外部系统的交互，如电子邮件，以及巨大的产品成本(从其他项目转移的资源，人们不想加入，因为他们不想记住另一个登录，安全事件发生时的公关问题)等等。

[混乱世界中的稳定性——Postgres 如何使事务原子化(2017)](https://brandur.org/postgres-atomicity)——深入探究 Postgres 的内部，以及它如何处理事务的提交/回滚逻辑。

[硬件如何推动数据库的发展(2017 年)](https://www.nextplatform.com/2017/08/15/hardware-drives-shape-databases-come/)—[下一个平台](https://www.nextplatform.com/)采访[迈克尔·斯通布雷克](https://en.wikipedia.org/wiki/Michael_Stonebraker)关于下一波数据库和运行它们的硬件。他发现 OLTP 和 OLAP 工作负载之间有明显的区别，前者完全转移到内存数据库，而后者保留在磁盘上，但使用特定于问题的存储，例如用于基本商业智能的列和用于科学和数字工作的阵列。

[编译器介绍(2017)](https://nicoleorchard.com/blog/compilers) -我把这个放在这里主要是为了好看的图形。这是一个真正意义上的介绍，因为你除了“嗨”之外不会说什么到编译器。

[关系数据库分片原则(2017)](https://www.citusdata.com/blog/2017/08/09/principles-of-sharding-for-relational-databases/) -从商业角度谈分片。你的业务类型，是 B2B、B2C 还是 [B2B2C](https://www.techopedia.com/definition/23169/business-to-business-to-consumer-b2b2c) 对你实际可以使用哪种切分有很大的影响。B2B 遥遥领先，因为每个客户几乎完全与其他客户分离，使得通过`customer_id`进行切分非常容易。另一方面，B2C，尤其是脸书或 Twitter 等社交应用，使得分片非常困难，因为实体之间有很多联系。仔细阅读，Citus 博客还有更多值得探索的地方。

[防止服务器过载:限制正在处理的请求(2017)](http://www.evanjones.ca/prevent-server-overload.html) -这是一篇关于管理服务器过载方法的粗略文章。当你不能很快投入额外的资源来解决问题时。核心思想是应该控制在给定时间内处理的请求数量*和*将传入的请求放入一个有限的队列中，如果队列已满，则快速丢弃它们。两者都限制了尾部延迟。第一个确保服务器不会因为工作而过载，而第二个确保在给定的时间内只有这么多的工作要做。让请求队列更加明确的想法也是我在其他地方看到的。现在，当你使用一个给定的框架时，请求来自哪里，你对它们有什么控制来做这样的事情，或者反压力等等，这都是不透明的。