# Elm 中函数的部分应用

> 原文：<https://dev.to/matt24ray/partial-application-of-functions-in-elm-ll>

*这篇文章最初发表在[www.dailydrip.com](http://www.dailydrip.com)上，作者是[简·克利莫](https://twitter.com/janklimo?lang=en)。*

我的一个朋友最近提出了一个非常有趣的问题。

> 如果一个函数有两个参数，为什么它的类型注释 *f: a - > b- > c* ？类似于 *f: (a，b) - > c* 的东西不是更有意义吗？

对 Elm 如何处理函数的深刻理解将会使答案变得清晰，并允许我们编写更好的代码。

### 一次一个论点

让我们举下面这个例子:

```
greeting : String -> String -> String
gretting hello name = hello ++ ", " ++ name ++ "!" 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，这看起来像一个带有两个参数并返回一个结果的函数:

```
greeting "Hello" "DailyDrip" == "Hello, DailyDrip!" 
```

Enter fullscreen mode Exit fullscreen mode

然而，Elm 中的函数总是只接受*一个参数并返回一个结果(因此有了函数类型注释语法)。*

向我们的 greeting 函数传递一个参数会返回一个新函数，该函数接受一个参数并返回一个字符串(下面示例中被注释掉的输出来自 elm-repl):

```
helloGreeting = greeting "Hello"
-- <function> : String -> String 
```

Enter fullscreen mode Exit fullscreen mode

向这个部分应用的函数传递一个额外的参数将产生预期的结果:

```
helloGreeting "DailyDrip"
-- "Hello, DailyDrip!" : String 
```

Enter fullscreen mode Exit fullscreen mode

这表明下面的符号是等价的:

```
greeting "Hello" "DailyDrip" == ((greeting "Hello") "DailyDrip") 
```

Enter fullscreen mode Exit fullscreen mode

括号是可选的，因为默认情况下函数求值关联到左边。让我们看一些部分应用特别有用的例子。

### 表情函数定义

让我们从一个简单的将一个数翻倍的函数的例子开始。我们的第一次拍摄可能是:

```
double n = n * 2
-- <function> : number -> number 
```

Enter fullscreen mode Exit fullscreen mode

Elm 的美妙之处在于，即使是操作符也是函数:

```
(*)
-- <function> : number -> number -> number 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用部分应用在达到相同结果的同时更加简洁:

```
double = (*) 2
-- <function> : number -> number 
```

Enter fullscreen mode Exit fullscreen mode

因为 **(*)** 取两个数作为参数，编译器会推断我们的 double 函数取一个数作为唯一参数。

而将列表的所有元素加倍的函数可以写成:

```
doubleList list = List.map double list
-- <function> : List number -> List number 
```

Enter fullscreen mode Exit fullscreen mode

运用同样的原理，我们可以做得更好:

```
doubleList = List.map double
-- <function> : List number -> List number 
```

Enter fullscreen mode Exit fullscreen mode

### 配管

现在我们知道操作符也是函数，我们可以完全理解管道是如何工作的:

```
(|>)
-- <function> : a -> (a -> b) -> b 
```

Enter fullscreen mode Exit fullscreen mode

这并不神奇:第二个参数是一个函数，这使得它成为部分应用程序最常见的用例之一。演示它的一个很好的例子是下面的函数，它返回所有存款的总和:

```
amountDeposited : List Transaction -> Float
amountDeposited list =
    List.filter (\t -> t.type_ == Deposit) list
        |> List.map .amount
        |> List.sum
    -- rather than the nested equivalent:
    -- List.sum (List.map .amount (List.filter (\t -> t.type_ == Deposit) list)) 
```

Enter fullscreen mode Exit fullscreen mode

Elm 有一个重要的设计特性使得这一切成为可能:[数据结构总是最后一个参数](http://package.elm-lang.org/help/design-guidelines#the-data-structure-is-always-the-last-argument)。这使得使用管道和部分应用程序编写更好、更干净、更有表现力的代码变得轻而易举。