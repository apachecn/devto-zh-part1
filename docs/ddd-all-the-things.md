# DDD 所有的东西

> 原文：<https://dev.to/alexeyzimarev/ddd-all-the-things>

*本文原载于[媒体](https://medium.com/@zimareff/ddd-all-the-things-b075be2107dc)。*

[![](img/4d011d809fe362afd89bc75c35991d5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cTHQzo1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f1v3fpbqk4jtgne2n8u9.png)

几年来，随着领域驱动设计(DDD)越来越受欢迎，我不断听说:

*   DDD 很难
*   DDD 仅适用于核心域

这个咒语变得非常流行，并且经常被(错误地)用来鼓吹人们为了避免咀嚼领域知识而做的一些疯狂的事情。

在这篇文章中，我想回到根本。在几乎所有与 DDD 有关的演讲中，格雷格·杨都提到，在他看来，蓝皮书的章节顺序是不正确的。问题是大部分的战略设计都放在了书的最新章节。由于这本书本身并不是最容易阅读的，大多数人很少能阅读超过 50%,这意味着他们设法了解了这种无处不在的语言的基础，了解了一点上下文，然后花大部分时间阅读实现模式。

但是，如果你看看埃里克·埃文斯的推特，你会看到这个:
[![](img/935a58400a17c05c3f26dcb96422fd99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XqbSMH-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A-CVWo701QXf-H8LbKjlLog.png)

> “领域语言学家”@ domainlanguage.com

事情开始明朗了，不是吗？埃里克不是“聚合之王”或“储存库大师”。他是一名领域语言学家。

Vaughn Vernon 为我举办的(强烈推荐的)实现领域驱动设计研讨会的主要收获之一是:

> 领域驱动设计正在有限的上下文中开发无处不在的语言。

[![](img/7ea55c6acce8df723870b2d19712c32e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--peuqdzgA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AanxviasR1nf7Q6l879GaKw.png) 
那有多疯狂？而不是“找到正确的聚合边界”或“分层”。这是语言和语境。

你可能会问，它在现实生活中意味着什么，它与文章标题有什么关系？嗯，下面是我的回答:
处处使用领域驱动设计。从知识消化开始，尽可能多地获取该领域的知识。与领域专家交谈，使用事件风暴、思维导图和观察等技术获取知识。归根结底，我们都同意开发人员 90%的工作是找出要做什么，只有 10%是编码。DDD 将让你明白从问题空间的角度，从你的用户的角度该做什么。

通过写这个*，我并不建议*在任何地方都使用实现模式，比如聚合、存储库等等。这是完全错误的。但是，两件事——**语言**和**背景**——是您创建未来解决方案战略地图的主要工具，将在您的用户问题和您提议的解决方案之间建立这一缺失的联系。

每个上下文可以使用适合于该上下文的任何实现技术，可以是 CRUD、活动记录、事件源或任何其他适用的技术，以及与上下文本身的复杂性相一致的技术。然而，认识到你有不同的上下文，你的系统几乎从不只有一个上下文，你需要理解你的用户的语言，这将帮助你建立一个更好的系统。

2017 年 3 月，我在 [DDD 挪威聚会](https://www.meetup.com/dddnorway/)上做了一个关于这个的演讲，你可以在 [SlideShare](https://www.slideshare.net/alexeyzimarev/ddd-what-why-how-74199460) 上找到幻灯片。

同样的演讲被压缩到了 2017 年伦敦 DDD 交易所的 10 分钟闪电演讲中，你可以在 SkillsMatter 网站上观看。

你同意还是不同意？你对此有自己的想法吗？我很乐意讨论。