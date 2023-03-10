# JavaScript 中的匿名递归

> 原文：<https://dev.to/simov/anonymous-recursion-in-javascript>

```
(
  (
    (f) => f(f)
  )
  (
    (f) =>
      (l) => {
        console.log(l)
        if (l.length) f(f)(l.slice(1))
        console.log(l)
      }
  )
)
(
  [1, 2, 3]
) 
```

Enter fullscreen mode Exit fullscreen mode

是的，有这样的事情，我认为这将是一个有趣的例子来分享。其特点:[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming))、[自执行函数](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)、[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)、[函数编程](https://en.wikipedia.org/wiki/Functional_programming)、[匿名递归](https://en.wikipedia.org/wiki/Anonymous_recursion)。

您可以在浏览器的控制台中复制/粘贴上述示例。输出如下:

```
[ 1, 2, 3 ]
[ 2, 3 ]
[ 3 ]
[]
[]
[ 3 ]
[ 2, 3 ]
[ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

说到函数式编程，下面是类似的例子在[Scheme](https://en.wikipedia.org/wiki/Scheme_(programming_language))(JavaScript 受其影响的语言之一):

```
(
  (
    (lambda (f) (f f))
    (lambda (f)
      (lambda (l)
        (print l)
        (if (not (null? l)) ((f f) (cdr l)))
        (print l)
      )
    )
  )
  '(1 2 3)
) 
```

Enter fullscreen mode Exit fullscreen mode

## 平仓

像在许多其他编程语言中一样，调用一个函数是通过在它的名字
后附加圆括号`()`来完成的

```
function foo () { return 'hey' }
foo() 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，我们可以将任意数量的表达式放在括号中:

```
('hey', 2+5, 'dev.to') 
```

Enter fullscreen mode Exit fullscreen mode

上面片段的结果是`'dev.to'`。原因是因为 JavaScript 返回最后一个表达式作为结果。

将单个匿名(lambda)函数放在括号`()`中意味着结果将是[匿名函数](https://en.wikipedia.org/wiki/Anonymous_function)本身:

```
(function () { return 'hey' }) 
```

Enter fullscreen mode Exit fullscreen mode

这本身并不是很有用，因为匿名函数没有名字，除非我们在初始化时立即调用它，否则我们无法引用它。

像常规函数一样，我们可以在它后面附加括号`()`来调用它:

```
(function () { return 'hey' })() 
```

Enter fullscreen mode Exit fullscreen mode

箭头功能也是如此:

```
(() => 'hey')() 
```

Enter fullscreen mode Exit fullscreen mode

同样，在匿名函数后附加括号`()`意味着我们正在执行它，也称为[自执行函数](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)。

## 关闭

一个[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming))是一个函数和声明该函数的词法环境的组合。结合[箭头功能](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)我们可以这样定义:

```
var foo = (hi) => (dev) => hi + '  ' + dev 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器的控制台中调用上述函数将打印`'hey dev.to'` :

```
foo('hey')('dev.to') 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们可以从封闭函数的外部作用域访问内部作用域中的`hi`参数。

以上代码与
相同

```
function foo (hi) {
  return function (dev) { return hi + '  ' + dev }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且自动执行版本将是:

```
(
  (hi) =>
    (
      (dev) => `${hi}  ${dev}`
    )
    ('dev.to')
)
('hey') 
```

Enter fullscreen mode Exit fullscreen mode

首先，`hey`参数被作为`hi`参数传递给上述函数的最外层作用域。然后，该函数返回另一个需要首先评估的自执行函数。然后，`dev.to`参数作为`dev`参数传递给最内层的函数，该函数返回最终结果:`'hey dev.to'`。

## 深入

下面是上述自执行函数的一个略微修改的版本:

```
(
  (
    (dev) =>
      (hi) => `${hi}  ${dev}`
  )
  ('dev.to')
)
('hey') 
```

Enter fullscreen mode Exit fullscreen mode

首先，`hey`参数被作为参数传递给最外层的作用域，但是这里没有传递函数，而是传递了另一个需要首先计算的表达式。因此，`dev.to`参数作为`dev`参数传递给内部自执行函数，并返回另一个函数。最后一个函数满足最外层的作用域，因此接收`hey`参数。

需要注意的是，[自执行函数](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)和[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming))用于初始化和封装状态，这也是我们在下一个例子中要用到的。

## 匿名递归

回到我们最初的例子，这次注释为:

```
(
  (
    (f) => f(f) // 3.
  )
  (
    (f) => // 2.
      (l) => { // 4.
        console.log(l)
        if (l.length) f(f)(l.slice(1))
        console.log(l)
      }
  )
)
(
  [1, 2, 3] // 1.
) 
```

Enter fullscreen mode Exit fullscreen mode

1.  输入数组`[1, 2, 3]`被传递到最外层的作用域
2.  整个函数作为参数传递给上面的函数
3.  这个函数接收底部的一个作为参数`f`并用它自己调用它
4.  在`3.`中调用`2.`导致返回满足最外层范围的`4.`函数，因此接收输入数组作为`l`参数

这样做的原因是为了在接收输入数组`l`的递归函数中引用`f`函数。我们可以这样称呼它:

```
f(f)(l.slice(1)) 
```

Enter fullscreen mode Exit fullscreen mode

注意,`f`是一个闭包，所以我们需要用它本身来调用它，以便访问对输入数组进行操作的最内部的函数。

出于解释的目的，第一个`console.log(l)`语句表示递归的自顶向下，第二个表示递归的自底向上。

## 结论

我希望你喜欢这篇文章，并从中学习到一些新的东西。闭包、自执行函数和函数式编程模式不是魔术。他们遵循简单的原则，易于理解，玩起来很有趣。

也就是说，你必须培养自己的意识，什么时候使用它们，或者不使用它们。如果你的代码变得难以维护，那么稍微重构一下可能是个好主意。

然而，理解这些基本技术对于创建干净优雅的解决方案以及升级至关重要。

编码快乐！