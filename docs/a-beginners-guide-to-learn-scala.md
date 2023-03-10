# Scala 编程语言初学者指南

> 原文：<https://dev.to/bmadusudanan/a-beginners-guide-to-learn-scala>

我接触 scala 已经有一段时间了，我从朋友/同事/其他人那里得到的大多数问题是

*   scala 为什么难？
*   如何用简单的方法学习 scala？
*   学习 scala 值得吗？

在这篇文章中，我介绍了我走过的学习之路，一路上犯的错误以及我学到的东西。

我将主要关注 java 开发人员的道路，因为他们是大多数，但是其他开发人员也可以从这篇文章中学到很多。

#### 一点背景知识

scala 的历史在 [scala lang wikipedia](https://en.wikipedia.org/wiki/Scala_(programming_language)) 文章中有很好的总结。创建 scala 背后的主要动机是创建一种运行在 JVM 上的面向对象的函数式多范例编程语言。我们都知道，现在许多语言都将 JVM 作为其性能、广泛使用等的目标。,

其他一些针对 JVM 的语言有 [clojure](https://clojure.org/) 、 [Ceylon lang](https://ceylon-lang.org/) 、 [Kotlin](https://kotlinlang.org/) 、 [Groovy](http://groovy-lang.org/) 、 [Eta lang](http://eta-lang.org/) 等。，当然除了 java。但是 scala 有自己独特的方式。

#### Scala 作为第一编程语言

这种错误往往是因为[流行语先设计](https://twitter.com/thepracticaldev/status/721716958769635328?lang=en)的思维定势。Scala 绝对不是一门对初学者友好的语言，它也不想成为这样的语言。不是说不可能，而是把这么多东西塞进一门语言里是没有意义的。结果将是一切都变得一团糟。

这并不令人鼓舞，但不要灰心丧气。与流行的神话相反，scala 可以通过适度的努力学习。如果你是一个没有编程经验的初学者，那么有一些 MOOCs 可以教你基本编程。我所知道的最好的一个是 [CS50](https://cs50.harvard.edu/) 。然后你可以学习一点点 python，然后进步到 java。

理论上任何 OOP 语言都可以，比如 Python/Ruby/C#。

#### 先学 OOP 这边

现在你已经准备好接受 scala 了。人们通常会犯的下一个大错误是学习它的功能性方面，并被它淹没。Scala 不应该受到指责。我们应该责怪我们在本科课程中所学到的东西。几乎所有的大学本科课程都不教授函数式编程的基础。结果，我们的风格被固定在一个很难理解的命令式/面向对象的范例上。

在 scala 中也很有可能进行面向对象/命令式的编程。这种多范例风格使得学习函数式编程非常完美，而且不会掉太多头发。我强烈建议学习 scala 面向对象的一面，将小代码片段/算法从 java 转换到 scala。

因为 scala 运行在 jvm 上，所以它可以使用 java 库，只需做很小的改动。尝试在其中使用 java 库。有趣的是，由于 scala 的编程语法，scala 中的结果代码已经更加简洁/简短。

在尝试核心 scala 库之前做这个步骤，因为它们更支持函数式编程。scala 的开发者并没有开发出与 java 中的命令式/oop 库等价的东西，比如 spring/hibernate/gson 等等。，因为您可以在 scala 中直接使用它们的 java 对应物。语言互操作性是一个巨大的优势，我们必须充分利用它，而不是仅仅为了时髦或者酷而使用核心 scala 库。

#### 熟悉生态系统

很多人低估了这一步。你应该对工具/生态系统感到满意，这一点非常重要，因为这会让你更加沮丧。由于 scala 工作在 jvm 之上，与 java 的主要区别显然是编译器，然后可能是不同的 IDE。

大多数 scala 程序员使用 [Intellij](https://www.jetbrains.com/idea/) + Scala 插件、 [Eclipse](http://scala-ide.org/) 作为他们的 ide。如果你是一个文本编辑器爱好者，有一个叫做[的东西支持主要的文本编辑器。](http://ensime.org/editors/)

我们可以对项目使用传统的 [maven](https://maven.apache.org/) 构建工具，但建议使用 [scala 构建工具](http://www.scala-sbt.org/)或简称 sbt。Sbt 提供了一种更简单的方法来处理依赖关系，并且您可以不费吹灰之力就开始学习。

Scastie 是一个分享代码片段的便捷工具。有点类似于 jsfiddle。

#### 接近函数式编程

由于 scala 在基础层面上结合了 OOP 和 FP，我们可以在代码库中懒洋洋地包含 FP 风格。从我个人的经验来看，我会采取以下的路线。

Java -> Scala OOP(渐进迁移-因为它们可以互操作)
Scala OOP - > Scala FP(可以基于项目需求渐进/完全重构)

在我看来，这是 scala 大放异彩的原因，因为在开发/重构的每个阶段，如果处理得当，它可以一部分一部分地完成，而不会打破常规的开发周期。

[![Functional programming](img/c9d49c89769a665e72b280326aec410e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TnC1zQ8i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbfqem10vfmoj51sxtgt.jpg)

#### 为什么要学习 scala？

那么，为什么要学习一门很难学又会耗费你大量时间的语言呢？许多人通常倾向于学习新的技术/语言来获得职业发展/进入这个行业，对于这个 scala 来说是非常好的。但是除此之外，函数式编程确实可以让你成为一个更好的程序员，即使你在日常工作中不使用它。它让你以你以前从未想过的方式思考，把它用于实际目的的决定取决于你，因为你也可以用 OOP 风格编程。

同样优选的是，

*   机器学习(Apache spark mllib)
*   大数据(Apache spark、Apache kafka)
*   [前端开发](https://www.scala-js.org/)
*   它现在也有了一个本地运行时，所以你不再局限于 JVM，你可以利用本地编程库。顺便说一句，Scala native 还处于早期阶段。

我已经写了一篇关于为什么 scala 会是你应该考虑的语言的文章。一定要读一读。

#### 学习 scala 的资料

与许多其他语言不同，scala 没有一个标准的学习路径来适应有经验的函数式程序员/新手。我根据材料的格式对它们进行了分类。

[![I should learn scala](img/81272b3d6a483819250343d64b7dedc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---0jx5MYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/1otzzy.jpg)

MOOC

*   [Scala 中的函数式编程专门化](https://www.coursera.org/specializations/scala)。我不认为这是初学者友好的，但是非常全面，尽管来自 scala 的创始人自己

<cite>书籍</cite>

*   [斯卡拉朗图书](https://www.scala-lang.org/documentation/books.html)。这几乎是你想要的列表。所有这些书都非常详细，制作精良。

<cite>博客</cite>

*   [Scala 新手指南](http://danielwestheide.com/scala/neophytes.html)。在许多核心函数式编程主题中，这无疑是我的最爱之一。
*   易浩的博客。scala js 的创造者。
*   来自 scala lang 作者自己的博客
*   [Scala 教程系列](https://madusudanan.com/tags/#Scala)。我正在写一个博客系列，旨在用和我上面描述的完全一样的方式学习 scala。但我不是完美的作家，YMMV。

学习困难是非常主观的。有些人觉得数学很容易，有些人觉得不容易。最好的方法是你自己决定，记住人们已经经历了什么。

[![Yoda much to learn](img/7888a9c327a75119fbfca187bdc875f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nnxpGpfr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6tu0z4b0h9al59iwi6cd.jpg)

请在这篇文章中指出任何错误/错别字/任何可以改进的地方。