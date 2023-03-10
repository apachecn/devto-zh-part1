# 关于总线因子(3/5)的经验教训:编写显式代码

> 原文：<https://dev.to/einenlum/lessons-learned-about-bus-factor-35-write-explicit-code-38l>

*这一系列文章摘自我在[柏林 PHP 会议](https://twitter.com/bephpug)上的演讲，最初发布在我的[博客](https://www.einenlum.com)上。如果你没有读第一本书，[给你](https://www.einenlum.com/articles/bus-factor-1)。*

* * *

Dev.to 对我的观点有些异议。它扰乱了他们！你绝对应该在这里阅读这篇文章[。](https://www.einenlum.com/articles/bus-factor-3)

## 写显式代码

上一次我们谈到了在当前的编码团队中可以建立的知识共享策略。这很好，但如果能帮助任何人阅读你的代码并马上理解你的实现，那就更好了。**让我们深入一些具体的代码。**

假设你是一个项目的新手，你必须在下面的 PHP 代码中添加一个新特性。我知道，是 PHP。但是不要离开。这是一个基本的问题，即使你讨厌这种语言，理解这是怎么回事也不是问题。这些考虑应该适用于几乎任何语言和代码。这是我写的一个虚拟例子，反映了当你加入一个随机项目时，你可能会发现的那种常见的坏代码。

试着在几秒钟内理解这段代码的内容。