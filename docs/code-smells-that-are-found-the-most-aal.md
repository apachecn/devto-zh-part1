# 发现最多的代码气味

> 原文：<https://dev.to/apium_hub/code-smells-that-are-found-the-most-aal>

在 [Apiumhub](/) 中，我们始终关注[软件开发](https://apiumhub.com/web-development-barcelona/)中的质量和最佳实践。当我们没有从头开始一个项目时，我们经常会发现代码的味道，这篇文章就是关于它的。Martin Fowler 有一天很好地解释了什么是代码气味，它是一种表面迹象，通常对应于软件系统中更深层次的问题。这个术语是由肯特·贝克在帮助马丁写《重构》一书时首次提出的，我强烈推荐阅读这本书。嗯，如果你对这个话题感兴趣，在这里你可以找到其他非常有用的[软件开发](https://dev.to/apium_hub/20-essential-software-development-books-to-read)和[软件架构书籍](https://dev.to/apium_hub/top-19-software-architecture-books-blf)的列表。
代码气味的好处之一是，没有经验的人很容易发现它们，即使他们不知道如何评估是否存在真正的问题或纠正它们。许多公司组织“每周代码气味”,让开发人员寻找气味，并向团队的高级成员提出。一次只闻一种味道是一个逐渐教会团队成员成为更好的程序员的好方法。

常见代码气味

开发人员通常被训练去寻找意外引入到他们代码中的逻辑错误。这种错误包括从没有被处理的被遗忘的边缘案例到导致整个系统崩溃的逻辑错误。但是其他不影响系统运行方式的问题呢？例如，使系统难以维护的设计问题，以及增加将来出现错误的机会等。？代码气味是你的代码[应该被重构](https://dev.to/apium_hub/code-refactoring-techniques-4pp)以提高可扩展性、可读性和可支持性的信号。

**这里有[最常见的代码气味](https://sourcemaking.com/refactoring/smells) :**

膨胀器是指代码、方法和类已经膨胀到难以处理的程度。通常这些气味不会马上冒出来，而是随着程序的发展而逐渐积累。比如:长方法，大类，原语执念，长参数表，数据丛生。

**面向对象滥用者**

所有这些气味都是对面向对象编程原则的不完整或不正确的应用。例如，Switch 语句、临时字段、拒绝遗赠、具有不同接口的可选类

这些气味意味着，如果你需要在代码的某个地方做一些改变，你也必须在其他地方做很多改变。结果，程序开发变得更加复杂和昂贵。例如:不同的变化，猎枪手术，并行继承层次结构

可有可无是指一些没有意义和不需要的东西，没有它们代码会更干净、更高效、更容易理解。比如:注释，重复代码，懒类，数据类，死代码，投机取巧。

这组中的所有气味都有助于类之间的过度耦合，或者表明如果耦合被过度委托所取代会发生什么。比如，特征羡慕，不恰当的亲密，消息链，中间人，不完整的图书馆类。

**我们来详细看看其中的一些，被发现最多的:**

**长法**

程序员的大部分时间是花在阅读代码上，而不是写代码。除了在阅读一个长方法时必须记住大量复杂逻辑的困难之外，这通常是该方法有太多责任的一个标志。长方法使得代码难以维护和调试。如果不可能在你的智能手机屏幕上看到整个方法，考虑把它分成几个更小的方法，每个方法做一件精确的事情。

**重复代码**

当开发人员修复了一个 bug，但随后又面临同样的症状时，这可能是代码重复的结果，一个 bug 在一次出现的不完美代码中得到修复，但在重复的版本中却没有。这造成了维护方面的开销。当开发人员没有意识到重复时，他们只知道修复他们遇到的重复。注意重复的代码块，将它们提取到一个地方——不要重复！

**继承方法**

如果一个类继承自一个基类，但不使用任何继承的字段或方法，开发者应该问问自己，继承是否真的是正确的模型。这种代码味道的迹象可能是继承的方法没有被使用，或者被空的方法部分覆盖。

当一个类想要重用其超类中的代码时，应该使用继承。如果类发生了分歧，子类不再需要该功能，那么应该打破层次结构，考虑委托。为了保留一些继承性，从子类中移除未使用的字段和方法，并创建一个新的层，对象可以从中继承。

**数据块**

当多个方法调用采用相同的一组参数时，这可能是这些参数相关的迹象。要将一组参数放在一起，将它们组合在一个类中会很有用。这有助于组织代码。

当一个类的存在只是为了委托给另一个类时，开发者应该问问自己它的真正目的是什么。有时这是重构任务的结果，逻辑已经逐渐从类中移出，留下一个几乎空的外壳。

**原始类型**

原始类型在领域上下文方面给出很少。将它们包在一个小类中来代表这个想法。通常这种类型的类会被扩展以包含添加到类中的方法。

**发散代码**

它是当一个类由于不同的原因，通常以不同的方式发生变化，并遭受多种变化时。所以，理想情况下，您应该在公共变更和类之间有一对一的联系。

**霰弹枪手术**

基本上就是当你想做一种改变的时候，你需要对很多不同的职业做很多小的改变。问题是，当变化到处都是时，它们很难被发现，很容易错过一个重要的变化。

特性羡慕

当一个方法没有利用它所属的类中的数据或方法时。相反，它需要来自不同类的大量数据或方法。

**原始执念**

当你用多个原始数据类型来表示一个概念比如用三个整数来表示一个日期。不要害怕使用小对象来完成小任务，比如结合了数字和货币的货币类。

懒惰的类

一个做的还不够的类，但是记住你创建的每一个类都需要花钱来维护和理解。

**类型嵌入在名称中**

避免将类型放在方法名中；这不仅是多余的，而且如果类型发生变化，它还会迫使您更改名称。

**无声的名字**

方法的名字是否简洁地描述了该方法的功能？你能把这个方法的名字读给另一个开发人员听，并让他们向你解释它的作用吗？如果不是，重命名或重写它。选择一套标准术语，并在整个方法中坚持使用。比如你有“开”，你大概就应该有“关”。

**死代码**

删除不被使用的代码。让它变得简洁明了。

如果你愿意，我们可以在下面的评论区讨论它们。

如果您对软件开发的最佳实践感兴趣，我强烈推荐您[订阅我们的每月简讯](http://eepurl.com/cC96MY)以接收最新的软件开发书籍、技巧和即将到来的活动。

## 如果你喜欢这篇关于常见代码气味的文章，你可能会喜欢…

[2018 年要读的软件开发书籍](https://dev.to/apium_hub/20-essential-software-development-books-to-read)
[为什么是 Kotlin 语言？谷歌为什么选择它？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)
[iOS 与浪子的持续集成&Jenkins](https://dev.to/apium_hub/ios-continuous-integration-with-fastlane--jenkins-19n)
[iOS 中的 MVP 模式](https://dev.to/apium_hub/mvp-pattern-in-ios-98f)
[今年要读的软件架构书籍](https://dev.to/apium_hub/top-19-software-architecture-books-blf)
[Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)
[F-bound over 类属 Scala 类型](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)
[Charles Proxy in Android Emulator](https://apiumhub.com/tech-blog-barcelona/charles-proxy-android-emulator/)
[顶级软件开发博客](https://apiumhub.com/tech-blog-barcelona/top-software-development-blogs-2017/)
[顶级软件测试技术](https://apiumhub.com/tech-blog-barcelona/top-software-testing-techniques/)  [功能债 vs 技术债](https://apiumhub.com/tech-blog-barcelona/technical-debt/)
[BDD: UI 测试](https://apiumhub.com/tech-blog-barcelona/user-interface-testing/)
[微服务 vs 单片架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)
[面向对象设计中的 SRP](https://apiumhub.com/tech-blog-barcelona/single-responsibility-principle/)
[几乎无限的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)
发现最多的帖子[代码气味](https://apiumhub.com/tech-blog-barcelona/code-smells/)最先出现在 [Apiumhub](https://apiumhub.com) 上。