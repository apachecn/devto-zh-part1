# Swift 中的功能组合

> 原文：<https://dev.to/danielinoa/function-composition-in-swift-ed2>

通常对一个值执行操作，并将该操作的结果值用作后续操作的参数，等等。

```
// Definitions

func square(_ x: Int) -> Int {
    return x * x
}

func increment(_ x: Int) -> Int {
    return x + 1
}

func half(_ x: Int) -> Int {
    return x / 2
}

func describe(_ val: CustomStringConvertible) -> String {
    return "The resulting value is: \(val)"
}

// Usage

describe(increment(square(half(6)))) // "The resulting value is: 10" 
```

Enter fullscreen mode Exit fullscreen mode

这工作得很好，但是代码很快开始看起来像是[球拍](https://racket-lang.org/)，并且变得很难解包。
做同样事情的另一种方法是利用函数组合，这需要将许多函数链接成一个函数。这种串联函数的想法可以通过利用泛型和中缀操作符在 Swift 中实现。

```
// Custom Operator Definition

precedencegroup Group { associativity: left }
infix operator >>>: Group
func >>> <A, B, C>(_ lhs: @escaping (A) -> B,
                   _ rhs: @escaping (B) -> C) -> (A) -> C {
    return { rhs(lhs($0)) }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个接受两个参数并返回闭包的函数。

*   `>>>`中的第一个参数是一个函数，它接受 1 个类型为`A`的参数，并返回一个类型为`B`的实例。
*   `>>>`中的第二个参数是一个函数，它接受 1 个类型为`B`的参数(这是连接发生的地方)并返回一个类型为`C`的实例。
*   最后，`>>>`返回另一个函数，它接受 1 个类型为`A`的参数，并返回一个类型为`C`的实例。类型`B`既是第一个参数的输出，也是第二个参数的输入，是`A`和`C`之间的结。

*   函数体对`rhs`运行`lhs`的结果并返回。这些函数在内部是嵌套的，因此函数参数`lhs`和`rhs`可以在外部描述为一个序列。

然后我们可以做以下事情:

```
// Usage With Custom Operator

(half >>> square >>> increment >>> describe)(6) // "The resulting value is: 10" 
```

Enter fullscreen mode Exit fullscreen mode

这就变成了一种更好、更清晰的描述操作序列的方法，其中函数是链式的，而不是嵌套的。

感谢阅读！

*本帖最初发表于[danielinoa.com](http://danielinoa.com)T3】*