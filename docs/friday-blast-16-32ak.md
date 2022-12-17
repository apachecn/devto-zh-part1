# 星期五爆炸#16

> 原文：<https://dev.to/horia141/friday-blast-16-32ak>

[坚韧饼干(2017)](https://scotthelme.co.uk/tough-cookies/)——2017 年制作安全饼干指南。它们应该只有 http，安全，有相同的网站属性，并使用前缀。这首先防止了 XSS 和 XSRF 攻击，并确保 cookies 本质上被限制为用户状态的关联信息的角色。

[CSRF 死了(2017)](https://scotthelme.co.uk/csrf-is-dead/) -对 cookies 的同一网站属性的更深入探究。这是通过只允许给定 cookie 的附件请求与 cookie 相同的源来实现的。这有效地阻止了 XSRF 攻击。浏览器支持正在迅速建立，但在一段时间内，您仍将不得不使用一些旧方法。

[云容量规划工程师指南(2017)](https://increment.com/cloud/an-engineers-guide-to-cloud-capacity-planning/) -底线是您不应该太担心“精确的规划”，而应该担心应用程序和基础设施在规划时可以扩展。

[云存储服务的一致性模型(2016)](https://blog.cloudrail.com/compare-consistency-models-of-cloud-storage-services/) -各种云存储产品一致性模型的有趣概述。包括像 DropBox 或 Box 这样的消费者服务，对于这些服务，我以前从未真正考虑过一致性的问题。

[Web 开发变得怪异(2016)](http://ane.github.io/2016/10/25/web-development-has-become-weird.html)——对 spa 的咆哮。它有一些优点，尤其是关于重新发明轮子。但我确实认为，一个建造良好水疗中心比传统的水疗中心有更好的体验。也更有趣——更像一个 Android 或 iOS 应用程序，而不是一个“站点”。截至 2017 年，一些技术选择已经沉淀下来。不再是一周一个构建系统或者一个月一个包管理器的情况了。作者提到的“无聊”时期可能会更快到来。

[了解 HBase 和 BigTable (2008)](http://jimbojw.com/wiki/index.php?title=Understanding_Hbase_and_BigTable) -一个更老的读物，但仍然相关。使用 HBase 和 BigTable 这样的大型数据库很难获得经验，因为它们只在一定规模下发挥作用。因此，这类文章对积累相关经验很有用，因为你永远不知道什么时候一份工作可能需要它。这一个有一个非常好的增量方式来达到最终的 HBase/BigTable 数据模型，对于来自关系背景的人来说可能不太直观。