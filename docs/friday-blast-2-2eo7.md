# 周五爆炸#2

> 原文：<https://dev.to/horia141/friday-blast-2-2eo7>

在这篇文章中，我已经开始在链接旁边加上年份。当然，偏向将会是最近的文章。但有时了解一篇文章的背景对判断其内容/建议是有用的。

[十个常见的数据库设计错误(2007)](https://www.red-gate.com/simple-talk/sql/database-administration/ten-common-database-design-mistakes/) -有趣的是，许多错误都与使用数据库的开发过程有关。测试、文档、编码标准等。如果我们从总体上谈论软件开发，这个观点仍然成立。唯一没有经受住时间考验的建议是关于使用存储过程的建议。取决于你问的是谁，存储过程介于必要之恶和反模式之间。

[DNS:精彩部分(2013)](https://www.petekeen.net/dns-the-good-parts) -从应用程序员的角度快速概述 DNS。标题是对 [JavaScript 的赞美——好的部分](http://shop.oreilly.com/product/9780596517748.do)，尽管我认为 DNS 比 10 分钟的阅读更好。

[Ad Tech 简介(2017)](https://dev.adzerk.com/docs/glossary) -作为 Adzerk 文档的一部分，这是对 Ad Tech 业务方面的高级概述。该领域的程序员应该知道的基本知识，以及对任何好奇的人有用的信息。

[前端的后端(2015)](http://samnewman.io/patterns/architectural/bff/)——我最喜欢这篇文章的一点是，它为我之前遇到过几次的一种模式命名。前端的后端(BFF)是一种专门为特定前端提供数据的服务。虽然其他服务是 API，并且必须是客户端不可知的和“通用的”，但是 BFF 耦合到它所服务的客户端，并且甚至应该由客户端团队开发。我在[对前端](http://horia141.com/on-bffs.html)的后端有一些自己的看法。

[流式传输所有东西(2017) #talk](https://www.youtube.com/watch?v=Hjae0Cw9oew) -谈论最近为公司构建*数据基础设施*的流式传输方法。它也是卡夫卡的一个插件，但话说回来，卡夫卡似乎是一项相当不错的技术，所以我不太介意。要点是，在一个组织中发生的每一个事件——从页面浏览量到在工厂生产的小部件——都应该放在一个集中的流日志中，其他流程都可以在其中工作。

[国际盒子尺寸认知日(2014)](https://css-tricks.com/international-box-sizing-awareness-day/) 和[继承盒子尺寸可能稍好的最佳实践(2014)](https://css-tricks.com/inheriting-box-sizing-probably-slightly-better-best-practice/)——要点是你应该使用`box-sizing: border-box`作为所有元素的默认，因为它使一切都变得更好。

[Autoprefixer:以最佳方式处理厂商前缀的后处理器(2013)](https://css-tricks.com/autoprefixer/)——同样，要点是你应该使用 [Autoprefixer](https://github.com/postcss/autoprefixer) 作为你前端构建链的一部分，因为它为你做 CSS 厂商前缀*auto*——自动地和更多的知识。

[UUID vs BIGSSERIAL 争夺主键(2015)](http://thebuild.com/blog/2015/10/08/uuid-vs-bigserial-for-primary-keys/) 和 [UUID 还是 GUID 作为主键？小心点！(2017)](https://tomharrisonjr.com/uuid-or-guid-as-primary-keys-be-careful-7b2aa3dcb439)——两篇讨论 UUIDs 作为主键的利弊的文章。