# 评估失败和优化水平

> 原文：<https://dev.to/onmyway133/assertionfailure-and-optmization-level>

原帖[https://github.com/onmyway133/blog/issues/39](https://github.com/onmyway133/blog/issues/39)

我们曾经用`assertionFailure`来标记程序员的错误或者一些不应该发生的事情。

从[断言失败](https://developer.apple.com/reference/swift/1539616-assertionfailure)

> 指示内部健全性检查失败。
> 
> 当控制流预计不会到达调用时，使用此函数停止程序，而不影响发运代码的性能，例如，在默认的开关情况下，您知道必须满足其他情况之一。若要防止代码在发布版本中被无效使用，请参见 precondition failure(_:file:line:)。
> 
> *   在 playgrounds 和-Onone 构件中(Xcode 调试配置的默认设置)，在打印消息后，在可调试状态下停止程序执行。
> *   In -O 构建，没有效果。
> *   在未检查的构建中，优化器可能认为这个函数从未被调用过。不满足这个假设是一个严重的编程错误。

因此，转到您的目标设置，并检查`Optimization Level`，确保它不是发布配置的`-Onone`。

调试和发布的区别就是这个`SWIFT_OPTIMIZATION_LEVEL`。如果`-Onone`那么你的配置被认为是调试，并且`assertionFailure`会使你的应用崩溃

阅读更多

*   [http://blog . krzyzanowskip . com/2015/03/09/swift-asserts-the-missing-manual/](http://blog.krzyzanowskim.com/2015/03/09/swift-asserts-the-missing-manual/)