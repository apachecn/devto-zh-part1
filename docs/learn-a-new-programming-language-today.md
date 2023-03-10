# 今天就学习一门新的编程语言

> 原文：<https://dev.to/lucianomarisi/learn-a-new-programming-language-today>

语言是达到目的的手段。快速掌握编程语言允许开发者构建任何东西。我最近一直在学习 Java 来为一个 [Spring](https://spring.io/) 后端做贡献。

我发现，一旦学习了语言的基础，一个很好的学习方法就是发展一个循环学习细节的过程，然后将它们付诸实践。

[![](img/ffca9b24d8287e0ff3934f5eb0c26540.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qrz_cshA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/itlvkqokquwsqfwym4a5.png)

## 入门-了解核心原则

首先要做的是理解语言的核心概念和主要用例。如果可以的话，找一个有经验的工程师来指导你。通过这种方式，您可以涵盖普通的设置内容，并立即运行代码。在这里花一两天的时间，足够让你开始了解一切，但不要太多，否则你会感到无聊并失去动力。

问自己以下问题:

*   开发人员一般用什么工具，为什么？
    *   一个好的起点是询问什么是最适合使用的文本编辑器/ IDE。
    *   了解开发人员为什么要使用这些工具，以及其他人存在但他们不使用的东西。记住，如果你只有一把锤子，所有东西看起来都像钉子。
*   什么是[型系统](https://en.wikipedia.org/wiki/Programming_language#Type_system)？
    *   是静态[还是动态](https://en.wikipedia.org/wiki/Programming_language#Static_versus_dynamic_typing)？
    *   是[强打还是弱打](https://en.wikipedia.org/wiki/Programming_language#Weak_and_strong_typing)？
*   什么是[编程范式](https://en.wikipedia.org/wiki/Programming_paradigm)？
    *   是面向对象的，函数式的，等等？
    *   它是局限于一个特定的范例还是支持众多的范例？一门语言支持[多种范例](https://en.wikipedia.org/wiki/Programming_paradigm#Multi-paradigm)是很常见的
*   是[编译还是解读](https://en.wikipedia.org/wiki/Compiler#Compiled_versus_interpreted_languages)？
    *   有时候分工不是那么明确，参见[即时编译](https://en.wikipedia.org/wiki/Just-in-time_compilation)和[字节码编译](https://en.wikipedia.org/wiki/Bytecode)

以上大部分高层次的问题没有一个绝对的答案，这就是为什么探索这些问题和答案是关键。这将让你建立坚实的基础，在此基础上学习细节。

## 了解详情

在这一点上，你希望对这种语言做什么以及如何执行一些代码有一些模糊的概念。所以现在是时候去认真思考如何写它了。

*   学习一些语法，例如:
    *   变量和函数是如何声明的？
    *   有哪些可用的[控制流](https://en.wikipedia.org/wiki/Control_flow)？例如 if 语句，用于循环
    *   什么是核心数据结构，你如何创建它们？(例如数组和字典)
*   它有什么样的特点？例如:
    *   是否有[引用](https://en.wikipedia.org/wiki/Reference_type)和[取值](https://en.wikipedia.org/wiki/Value_type)类型？它们是如何使用的？
    *   你能定义接口/协议吗？
    *   如何编写并发代码？
    *   如果是强类型，它有泛型吗？
*   该语言的[标准库](https://en.wikipedia.org/wiki/Standard_library)中有什么？
*   人们遵循的主要惯例是什么？
    *   查看代码样式指南，[为](https://google.github.io/styleguide/javaguide.html) [示例](https://github.com/github/swift-style-guide)
    *   开发者依赖第三方库吗？如果有，最常见的是什么？例如，我发现现代 Java 代码库使用 [RxJava](https://github.com/ReactiveX/RxJava)

## 头先潜进去

理论是伟大的，但是我们从经验中学到更多。没有人通过阅读有关自行车的书来学习如何骑自行车。尝试以下方法之一以获得经验:

1.  在你的工作场所为一个现有的项目做贡献，[如果可以的话，与另一个开发者配对](https://en.wikipedia.org/wiki/Pair_programming)。
    *   我发现进入一个遵循良好实践的已建立的代码库是令人敬畏的。通过扩展现有代码或遵循现有模式，添加新的小功能会更容易。
    *   其他开发人员的代码审查将有助于快速指出问题。
2.  为现有的开源项目做贡献。对于项目来说，标记出[问题](https://github.com/fastlane/fastlane/issues?q=is%3Aopen+is%3Aissue+label%3A%22complexity%3A+you+can+do+this%22)是很常见的，外人可以从中发现问题，这是一个很好的起点。
3.  创建自己的项目。找到你热衷于创造的东西，用你正在学习的语言来创造它。

最后，走出你的[舒适区](https://en.wikipedia.org/wiki/Comfort_zone)，推动自己不断学习。

*我要感谢[艾米丽·伍兹](http://twitter.com/emilyw113)审阅这篇文章。*