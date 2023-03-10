# Swift 中的三元展开

> 原文：<https://dev.to/danielinoa/ternary-unwrapping-in-swift-903>

* * *

## 这个问题

我经常发现自己在写这样的东西:

```
let foo: Foo
if let unwrappedDependency = optionalDependency {
    foo = Foo(unwrappedDependency)
} else {
    foo = Foo(fallback)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我有一个需要实例化的`foo`,但是它的依赖项是一个必须首先处理的可选项。这很容易通过用一个`if-let`或`guard-let`语句解开所述可选的来解决。**简单，但是表达这样一个简单的语句流**需要太多的行(取决于你如何计数)，这可能会在整个代码库中重复多次。这需要一个更优雅的解决方案。

Swift 已经提供了一些很棒的构造，如三元和零合并运算符，它们有助于编写更具表现力的代码，并减少代码的冗长性，如上所述。

```
// Nil Coalescing Operator
let optionalNumber: Int? = ...
let number: Int = optionalNumber ?? nonOptionalfallback 
```

Enter fullscreen mode Exit fullscreen mode

```
// Ternary Conditional Operator
let condition: Bool = ...
let number: Int = condition ? 1 : 0 
```

Enter fullscreen mode Exit fullscreen mode

但是，这两个运算符都无助于满足以下内联语句的要求:

*   评估了可选的
*   为成功展开提供路径
*   如果展开失败，则提供一个路径

* * *

## 解

由于没有满足这些要求的运算符，我们可以利用 Swift 的能力来定义和实施一个新的运算符，通过组合三元条件运算符和零合并运算符来实现这一点。
我准备把它叫做**三元展开算子**，它看起来是这样的:

```
let _ = optional ?? { unwrapped in ... } | ... 
```

Enter fullscreen mode Exit fullscreen mode

它允许我们将上述流程简化成这样:

```
let foo: Foo = optionalDependency ?? { Foo($0) } | Foo(fallback) 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细分析一下:

*   在`=`和`??`之间，我们有一个要评估的可选值。
*   如果所述选项可以安全地展开，那么执行`??`和`|`之间的语句。这里我们有一个闭包，它转发未包装的可选对象，并用于创建预期返回类型的实例(在本例中是`Foo`)。
*   `|`之后的语句作为后备，也必须解析为预期返回类型的实例。

* * *

## 用例

在这个操作符可能有用的许多场景中，我发现它在用可选的。

而不是这样做:

```
// This approach is overwrought.
let optionalNumber: Int? = 1
let sentence: String?
if let number = optionalNumber {
    sentence = "My favorite number is \(number)"
} else {
    sentence = nil
} 
```

Enter fullscreen mode Exit fullscreen mode

还是这个:

```
// This approach force unwraps which isn't ideal.
let optionalNumber: Int? = 1
let sentence: String? = optionalNumber != nil ? "My favorite number is \(optionalNumber!)" : nil 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样做:

```
// This approach is concise and leverages the ternary unwrapping operator.
let optionalNumber: Int? = 1
let sentence = optionalNumber ?? { "My favorite number is \($0)" } | nil 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 引擎盖下

这个三元展开操作符实际上是 2 个`infix`操作符结合使用。

```
precedencegroup Group { associativity: right }

infix operator ??: Group
func ?? <I, O>(_ input: I?,
               _ handler: (lhs: (I) -> O, rhs: () -> O)) -> O {
    guard let input = input else {
        return handler.rhs()
    }
    return handler.lhs(input)
}

infix operator |: Group
func | <I, O>(_ lhs: @escaping (I) -> O,
              _ rhs: @autoclosure @escaping () -> O) -> ((I) -> O, () -> O) {
    return (lhs, rhs)
} 
```

Enter fullscreen mode Exit fullscreen mode

*   第一个操作符`func ??`有两个参数:一个可选参数和一个元组。这个函数`??`本身实际上作为一个操作符就足够了。然而，需要使用`func |`来改进风格，使之与三元运算符相匹配。
*   第二个操作符`func |`，通过获取 2 个闭包并在一个元组中返回它们来充当 facade；后被`??`使用。

`I`(输入)和`O`(输出)分别描述可选参数的类型和返回值的类型(用于赋值)。

* * *

## 关闭思绪

作为一名开发人员，我的首要任务之一是提高我编写的代码的可读性，尤其是如果它要由我的同事审查的话。我愿意相信三元条件运算符对`if-else`语句所做的，这个新运算符可以对`if-let-else`展开语句；帮助开发人员编写易于他人理解的代码。

然而，这种三元展开操作符最适合于这样的情况，例如当对象/值的实例化或赋值依赖于 1 个需要展开的可选依赖项时。对于更复杂的情况，坚持使用`if-let`和`guard-let`可能更明智。和往常一样，当代码的可读性确实可以提高时，这里描述的所有操作符最好谨慎使用，而不仅仅是为了编写花哨或不合理的简洁代码。

你觉得这很有趣吗？你想尝试一下吗？查看样品[游乐场](https://github.com/danielinoa/Ternary-Unwrapping)。

感谢阅读！

* * *

## 亦见

*   [零合并算子](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID72)
*   [三元条件运算符](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID71)
*   [自定义操作符](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-ID41)

* * *

*本帖最初发表于[danielinoa.com](http://danielinoa.com)T3】*