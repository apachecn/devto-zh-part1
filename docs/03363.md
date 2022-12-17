# 2017 年 Java 和 JVM 生态系统的状况

> 原文：<https://dev.to/bmadusudanan/the-state-of-java-and-the-jvm-ecosystem-in-2017-bna>

## 简介

Java 最初是在 1995 年开发的，它已经走过了很长的路。你们中的许多人可能已经有了 Java 的经验，但是这里有一个有趣的短片可以帮助你开始学习。

[https://www.youtube.com/embed/E3418SeWZfQ](https://www.youtube.com/embed/E3418SeWZfQ)

这是会议的一部分，所以如果你想的话，可以忽略最后的宣传片。

Java 一直是褒贬不一的话题。但是最近有一些关于语言和 JVM 生态系统的有趣的发展。我认为这些变化是 Java/JVM 开发方式的巨大转变，它可能会改变未来。

## 最近更新

1)更快地移动 Java

这篇博文引起了我的注意-[Mark Reinhold](https://mreinhold.org/blog/forward-faster)的《让 java 更快发展》。Mark 是 Oracle Java 平台组的首席架构师。

这篇博文的一些要点。

*   JDK 的发布周期将会变得更短。主要版本六个月。
*   对主要版本的长期支持。最初的计划是三年。
*   季度更新发布。

你可能会奇怪，六个月的时间还是很慢，但是对于一个如此庞大的语言/平台来说，如果能够实现，这是非常令人印象深刻的。

我鼓励你完整地阅读这篇文章。

2)甲骨文计划开源其 JDK

对于不熟悉 Java 或者没有用 Java 编程的人来说。有多种 JDK 可以用来开发。甲骨文 JDK，开放 JDK 和其他几个。维基百科的页面维护着他们的列表。

直到最近，甲骨文公司宣布，他们将开放他们的 JDK-[https://www.infoq.com/news/2017/10/javaone-opening](https://www.infoq.com/news/2017/10/javaone-opening)

这是一个大新闻，因为在社区中选择哪一个总是一个困惑。开放的 JDK 是最受欢迎的，但是有一个难题，甲骨文 JDK 有更好的功能和更新更快。这一切都将改变，在适当的时候，甲骨文 JDK 公司将与开放的 JDK 合并。

3) [Java EE 重启并捐赠给 Eclipse 基金会](https://sdtimes.com/industry-spotlight-java-ee-finally-gets-rebooted/)

Java EE 处于非常糟糕的状态，部分原因是特性更新非常慢，而且在很大程度上被忽略了。这不仅是一次重新启动/改变，它还转移到 Eclipse Foundation，这是一个更好、更开放的开发人员社区。

4) Java 9 和新的增强功能

Java 语言现在是第 9 版。Java 8 带来了很多特性，这个版本甚至更多。

快速功能概述-[http://www.baeldung.com/new-java-9](http://www.baeldung.com/new-java-9)
甲骨文官方文档-[https://docs.oracle.com/javase/9/whatsnew/](https://docs.oracle.com/javase/9/whatsnew/)

Java 现在有了 REPL，增加了很多很酷的函数式编程特性，比如闭包和 Lambda 表达式。

5) JVM 改进的 GC 算法

尽管有些人可能不同意 Java 是一种语言的好选择，但另一方面，JVM 可能是目前最稳定的跨平台运行时。在 Java 7 中，关于 GC 优化和调优，我们没有太多选择。Java 8 带来了 G1 GC 等等。Java 9 默认使用 G1GC，因为在许多用例中，G1GC 比并行 GC 好得多。

除此之外，还有另外两种算法即将出现。一个是用于多 TB 堆的 [ZGC](https://www.infoworld.com/article/3235391/java/zgc-large-heap-java-garbage-collector-may-go-open-source.html) ，另一个是用于低暂停 GC 的[谢南多厄](http://openjdk.java.net/projects/shenandoah/)。我们可能会更快地看到它们，因为 JDK 的发展正在加快。

总是有一个好的 GC 算法，你可以根据你的用例来选择。

6)更多有趣的项目在排队

作为开放 JDK 的一部分，有许多有趣的项目，但有两个引起了我的注意，那就是瓦尔哈拉项目和 T2 琥珀项目。它们共同带来了值类型、局部变量类型推理、改进的泛型等。,

这可能会改变我们使用 Java 的方式，并对 JVM 中的其他语言产生巨大影响。

7) JVM 语言

JVM 的特性部分提升了运行在它上面的语言，因为它们不必做很多语法上的工作，这意味着编译器要做更多的工作。

Scala、Kotlin、Clojure、Eta、Groovy 都是一些著名的语言。

其中 Scala 和 Kotlin 最为突出。有些人甚至认为 Kotlin 可能会取代 Scala(他们没有可比性)。更多信息请见下文。

## 猜测&的意见

这一节我提出一些我的观点，这些观点可能是错误的。欢迎在评论中提出不同意见和讨论。

**关于科特林的未来**

科特林语似乎是来自 Jetbrains 的人们的一种奇妙的语言。它在移动开发上对 Android 有官方支持，Spring 框架有 Kotlin 支持。但是这种语言的核心更像是 Java 的语法糖。目前，由于它的高级特性，它比 Java 好得多，但是考虑到 Java 语言本身正在提供许多现代语法和特性，它可能会迎头赶上。超过一点，差异甚至可以忽略不计。与 Java 相比，Kotlin 开发人员要少得多。

这与目前业内的情况正好相反，即科特林越来越受欢迎，但这也是我的观点。

**选择语言**

Groovy 似乎是最不受欢迎的。这是一种很好的语言，但它的活性正在稳步下降。如果你要选择一种语言，我会考虑以下几点。

*   Java，如果你是一个面向对象的编程商店。
*   Scala 如果你更喜欢函数式编程(FP) + OOP。
*   Eta lang，如果你喜欢纯粹的功能和/或 Haskell 爱好者。
*   Clojure 如果你喜欢动态类型的 FP 语言。
*   Kotlin 如果你对 Android 感兴趣，你需要比 Java 更好的东西，但不是那种需要很长学习曲线的东西。(这可能会像我上面建议的那样改变)
*   Groovy 目前被用作脚本语言。

## 期末备注

这就是我所有的更新。我从来没有像现在这样对 Java 和 JVM 感兴趣和乐观。这是成为 JVM 平台开发人员的最佳时机。

页（page 的缩写）请指出任何错别字或任何可以改进的地方。

感谢阅读。