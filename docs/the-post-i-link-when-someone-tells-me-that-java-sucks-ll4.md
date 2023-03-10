# 当有人告诉我 Java 很烂时我链接的帖子

> 原文：<https://dev.to/sammyrulez/the-post-i-link-when-someone-tells-me-that-java-sucks-ll4>

# 有人跟我说 Java 烂的时候我链接的帖子

我也有机会使用其他语言，但是我花了大部分时间在 Java 或基于 JVM 的语言上。我有机会使用其他语言(python 是我最喜欢的一种)。

在所有这些年里，我不得不处理很多“Java 很烂”的对话，与那些虔诚地热爱这种当下语言的人。所以我决定写下这篇文章，并在任何时候……一个糟糕的对话发生时链接它。我将提出我的观点，支持基于 Java/ JVM 的环境，并声明:

我不认为 Java 是世界上最好的语言，我喜欢学习新东西。在我写作的时候，*对于我正在开发的*来说是最好的(IMHO)选择。

这是人们试图说服我 Java 很烂的五大原因

Java 很慢，这是完全错误的。大数据解决方案(spark，hadoop)都是建立在 JVM 上的。说到互联网性能，Java 解决方案是继原生引擎之后性能最好的解决方案:在这里你可以查看一些[数据](https://www.techempower.com/benchmarks/#section=data-r12&hw=peak&test=fortune&l=nvnl)。也许在以前(10 年前)是真的。也许用 Java 构建的 GUI 应用程序比原生的要慢，但是这并不重要，因为原生的总是比虚拟环境快。

*   **Java 啰嗦:**我们在这里说什么呢？缺少句法糖？使所有属性私有而不是用 getter/setter 方法访问的历史传统？在现代 Java ( 1.8)中，你可以用静态导入编写非常简洁的代码来构建快速的 DSL、注释和注释解析器来构建元编程工具、闭包。一些设计选择有其历史原因。我来引用一下[在 Quora](https://www.quora.com/Why-is-Java-so-verbose) 上的这个回答:*Java 为什么这么啰嗦？*

> Java 背后的哲学是显而易见的，它坚持让程序员明确地输入他们的意思，而不是提供语法上的好处。例如，要创建一个派生类，你可以使用单词“extends”而不是 C++中的“:”。没有用户定义的运算符重载。Java 中的命名约定会导致很长的名字。等等。等等。这个想法是创造一种防止新手犯错和滥用的语言。Java 基本上是一种由专家为非专家设计的语言

*   **Java 是一种古老的语言(或者说“Java 是新的 Cobol”):**Java 1.8 拥有静态类型的面向对象特性的所有特性，外加一些函数式编程特性。也就是说，这也是一种非常成功的语言，所以有很多遗留代码，很多糟糕的代码(因为一种语言使用得越多，产生的糟糕代码就越多:稍后会有更多)。长话短说:是的，你可以用 Java 编写现代代码。您需要动态类型语言是有原因的。使用 Groovy。想要一种函数式编程语言，它是同类中的第一种，并且是业界使用最多的？试试 Scala！(我爱死了！).“创业公司不使用它”的说法显然是错误的。 [**我们为什么选择 Scala？复杂数据流的实时分析——瓦洛**_ 2016 年 8 月 10 日作者:瓦洛·戴夫《大数据和 Scala 在 IT 领域的份额》无可否认，It 领域的任何新产品都是如此，不…_www.valo.io](https://www.valo.io/2016/08/10/why-did-we-choose-scala/) [**什么样的公司会将 Java 作为软件开发的主要语言？**_ 回答(第 1 题，共 12 题):虽然脸书可能使用 C++和 PHP，但他们也广泛使用 Java。Java 是商业语言……_ www . quora . com](https://www.quora.com/What-kind-of-companies-use-Java-as-their-primary-language-in-software-development)

[PayPal 如何仅使用 8 台虚拟机扩展到每天数十亿笔交易](http://highscalability.com/blog/2016/8/15/how-paypal-scaled-to-billions-of-transactions-daily-using-ju.html)

[有哪些创业公司或科技公司在使用 Scala？](https://www.quora.com/What-startups-or-tech-companies-are-using-Scala)

**Framework X / Java EE 过度工程化:* *也许…不要用。有很多选择。Java EE 是各种各样的技术和开源框架，每天都有不同的选择出现。Java 的真正问题可能是产品(从商业应用程序源代码到开源库)随着时间的推移变得功能臃肿。我认为这是因为它们中的大多数都与企业开发有关，这种市场(我对此非常了解)是“添加功能”这种疯狂行为的诅咒。然后糟糕的设计也会发生。我在 Hibernate 这样的项目中看到过它:我从 1.28 版本就开始使用它，我目睹它变成了一个庞大的庞然大物，与数百个依赖项纠缠在一起。当这种情况发生时，人们可能会认为这是 Java 的错误……其实不是。

**Java 不是开源的/受到怀有邪恶意图的公司的支持:* *也许在过去的 10 年里，你一直住在一个只有有限互联网连接的移动房子里编写 PHP 脚本。既然如此，那就来看一看[这里](http://openjdk.java.net)向开着的 JDK。

如果你想了解 Java/JVM 生态系统的更多细节，可以看看 Geert Bevin [**撰写的这篇系列文章:Java 成功的 10 个原因:第一部分| zeroturnaround.com**_ 当我离开 Java 去开发 Eigenharp instruments 的软件时，我不得不开发跨平台的 real…_zeroturnaround.com](http://zeroturnaround.com/rebellabs/10-reasons-why-java-now-rocks-more-than-ever-part-1-the-java-compiler/)

这将是一篇“正在进行中的工作”的博文，因为我认为人们总是会找到新的理由来咆哮。