# 面向协议编程(PoP)

> 原文：<https://dev.to/itscoderslife/protocol-oriented-programming-pop-3fk8>

这篇文章是我学习 Swift 和编程范例时的博客文章系列的一部分。[关于函数式编程的第一部分可以在这里找到](https://itscoderslife.wordpress.com/2016/12/10/what-is-functional-programming/)。

**面向协议编程(POP)** 可以说是——*一种将**程序附着到接口而不是实现**原则*的方式——这是软件开发的基本设计原则之一。构建您的代码，首先定义接口，然后让类实现它。使用协议定义接口，然后让类实现它们。POP 只是 Swift 编程语言中接口编程的另一个技术术语。

是啊！使用 swift 中的“扩展”和协议，Swift 中的 POP 还有其他优势。

让我们用一个经典的例子来讨论这个问题——**形状及其类别**。

任何形状都有面积。每个形状都需要画在画布上。通常一个普通的程序员会想:

*   让我们有一个名为 Shape 的基类。
*   让我们从 Shape 类中派生出所有其他的具体类。

这种解决方案的第一个也是最重要的缺点是所有的具体类都继承了基类的所有东西，不管是否需要。此外，多重继承是不可能的，而且这是一个坏方法。

在现实世界中，随着产品的增长，它的代码也在增长，在一段时间内会产生大量的开销。

接下来是面向协议的编程——创建接口

这是 Xcode [playground @ github](https://github.com/itsdamslife/DesignPatterns/blob/master/SwiftPlaygrounds/ProtocolOrientedProgramming.playground/Contents.swift) 的链接——给出了一个 POP 的典型例子。请在继续理解优点之前浏览代码。

有些加分的流行:

*   **没有基类的开销**。
*   **协议扩展**是给**默认实现**的好方法。
*   类可以**符合多个协议**也使用它们的默认实现，没有继承的开销。
*   限制只能访问对象的特定功能。
*   Swift 中的**结构&枚举**可以符合**协议**。也可以定义仅类协议

```
[protocol TheClassOnlyProtocol: class { ... }] 
```

。

*   协议可以作为变量的**类型，这比用具体的类声明变量更加动态。**
*   在 Objective-C 中，委托像以前一样工作。
*   协议可用于**集合类型**
*   一个协议可以从多个其他协议继承(**多重继承**)

这是我对 POP 的理解。请在下面分享你的评论。

希望这篇文章对你有所帮助。