# 星期五爆炸#22

> 原文：<https://dev.to/horia141/friday-blast-22-4f93>

度假归来，带着一套较短的链接。尽管如此，还是有一些非常有趣的东西。

[地理空间索引:支持 Lyft 的 1000 万 QPS Redis 架构(2017)#视频](https://www.youtube.com/watch?v=cSFWlF96Sds) -了解 Lyft 使用的工具和方法，以便在整个系统中将 Redis 扩展到超过 1500 万 QPS。增长如此之快，以至于报告的标题落后于实际数字。这是一种有趣的高级算法设计方法(基于 S2 的地理哈希、围绕位置更新和读取的复杂业务规则)和针对 Lyft 特定问题定制的基本基础设施(具有一致哈希的代理、服务发现、健康检查)。

[关于搜索环境中性能的推理(2017) #video](https://www.youtube.com/watch?v=80LKF2qph6I) -再次深入 Bing 的搜索设计(或至少是它的某些方面)。与通常的倒排索引/后列表相比，布隆过滤器的使用非常有趣。我想说，从这个演讲中得到的最好的东西是作者估计几种情况下的性能的方式，以及即使从高层次的数字(我想服务 1000 万个文档)如何得到相当低层次的细节(这意味着我平均要进行 5 次内存访问)。这是在设计任何性能敏感的东西时应该考虑的东西。也是系统设计面试中经常出现的东西。

[为什么你的加密数据库不安全(2017)](https://blog.acolyer.org/2017/06/16/why-your-encrypted-database-is-not-secure/) -原来数据库在各种内存和磁盘缓存、访问日志等方面泄露了大量信息，本地攻击者可以利用这些信息来击败所谓的*加密数据库中的数据加密*。这甚至以*快照攻击*的形式出现，这是一种只能看到一个查询的攻击。“保护静态数据是一个难题”框中的另一项内容。

[服务所有权清单(2017)](http://codecapsule.com/2017/11/12/service-ownership-checklist/) -一个服务所有权清单的例子-当你为其他团队/外部用户运行服务时，你需要涵盖的所有基础。涵盖从项目管理到安全、日志记录、容量规划等主题。这个列表有点令人生畏，但是同样，它适用于更成熟的组织，这些组织可以花时间来正确地做这些事情。

[基于承诺的架构艺术(2015)](http://blog.rangle.io/the-art-of-promise-based-architecture/)——在 JavaScript 领域，从回调到承诺，再到这些承诺之上的`async/await`，有一个巨大的转变。这是一篇从转变开始的文章，强调了好处以及如何在新的世界中思考。