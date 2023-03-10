# 移动应用开发:分享 Kotlin 编码经验

> 原文：<https://dev.to/dianamaltseva8/mobile-app-development-sharing-experience-in-kotlin-coding>

一旦在 2016 年 2 月正式发布，Kotlin fastly 就成为了热门的编程语言。Kotlin 是一种静态类型的编程语言，由 JetBrains 开发，运行在 Java 虚拟机上。此外，它可以被编译成 JavaScript 源代码或 JVM 字节码。

基于 Kotlin 编程语言的定制移动应用程序开发提供了许多优势。首先，JetBrains 的主要目标是让它像 Java 一样快，并且容易学习。他们成功做到了。)其次，Kotlin 提供了精简语法、扩展特性、空指针安全和中缀符号。

其中一个重要的事实是，Kotlin 语言来自工业界，而不是学术界。它是为解决程序员的困难和操作挑战而开发的。

我们也支持这一技术趋势，并使用 Kotlin 进行了两次实验:

1.  将 Java 编写的项目转移到 Kotlin 语言
2.  Kotlin 的项目开发从零开始

我们想特别关注 Java 没有的 Kotlin 特性。以下是最有趣、最有用的科特林介绍:

*   Lambda 表达式+内联函数=性能自定义控制结构
*   扩展功能
*   零安全
*   智能强制转换
*   属性
*   伴随物体
*   主要构造函数

将 Java 项目转移到 Kotlin 编程语言

JetBrains 负责将 Java 类自动转换成 Kotlin 类的能力。进行不同项目类的转换，我们得到了以下结果:我们得到的 Kotlin 代码，平均比 Java 上的少 20-25%。

因此，我们确保 Kotlin 语言允许编写更优雅和简洁的代码。这在模型类中最为明显，因为 Kotlin 属性允许免除 getters 和 setters。

Smart Class 也让我们很高兴:即使是一个对编程一无所知的人，也能够将 Kotlin 编程语言编写的代码的简单性和复杂性与 Java 进行比较:

// Java 代码:

if(get activity()LoginActivity 的 instanceof)

((LoginActivity) getActivity())。寄存器()；

//科特林代码:

if(活动是 LoginActivity)

```
activity. register () 
```

Enter fullscreen mode Exit fullscreen mode

从头开始 Kotlin 语言的项目开发

Kotlin 上的产品开发给我们留下了更加美好的印象。我们认为首先应该描述 Kotlin Android 扩展。

每个 Android 应用程序开发人员都非常了解 findViewById()函数。毫无疑问，这是潜在错误和令人讨厌的代码的来源，难以阅读和支持。

虽然有几个可用的库提供了这个问题的解决方案，但是有些库依赖于运行时，并且需要为每个视图注释字段。

此外，Kotlin Android Extensions 插件使我们能够获得与这些库类似的体验，而不必添加任何额外的代码或交付任何额外的运行时。

在 Kotlin 类中，开发人员可以直接使用一个视图的 id，而不需要额外的初始化或导致所需的类型，因为这种语言会自己处理它。因此，作为一个例子，没有必要使用 ButterKnife 库进行依赖注入。

有人可能会反对侦听器的初始化占用很多空间，看起来不美观，而 ButterKnife 允许消除这个问题。尽管如此，Kotlin 编程语言在没有第三方库的情况下也能成功应对:Lambda 表达式和内联函数提供了编写简洁、直观、完美工作的代码所需的一切。

在我们的定制移动应用程序开发中，我们使用了许多实用程序类来避免加载带有额外逻辑的活动和片段。例如，不正确地在整个应用程序中完全实现 Toast 系统消息的显示。

这就是为什么我们把它放到了实用工具课上。然而，要显示此消息，应用程序开发人员必须始终传递一个上下文系统对象。多亏了 Kotlin 扩展函数，我们可以向上下文原生类添加方法。

Toast 消息显示的比较方法调用:
// Java 代码:

```
 ToastUtils.showMessage (context, “message”); 
```

Enter fullscreen mode Exit fullscreen mode

//科特林代码:

```
context.toast(“message”) 
```

Enter fullscreen mode Exit fullscreen mode

看起来很棒，不是吗？

因此，由于 Kotlin 为工程师提供的优势和巧妙的介绍，我们从 kot Lin 的写作中获得了积极的印象。我们相信这种编程语言的潜力，并计划在我们的定制移动应用程序开发中继续积累经验。

了解更多关于 Kotlin 项目实验和 Kotlin 优势的信息[https://smartym . pro/blog/sharing-experience-in-cot Lin/](https://smartym.pro/blog/sharing-experience-in-coding-in-kotlin/)。

* * *