# 普拉特分析

> 原文：<https://dev.to/jrop/pratt-parsing>

从开始编程开始，我就努力为自己想出有趣的项目，哪怕纯粹是为了教育目的。编程的乐趣有时是目标。再加上一个学习新概念的机会，你已经引起了我的注意！我最初的一些项目非常简单:

*   想学 Java Swing 的时候，我创造了一个猜数游戏。(“我想的是 1 到 10 之间的一个数字...你有 10 次机会猜它”...)
*   当我想学习如何与 XML 交互时，我在。NET，我用它和我的兄弟姐妹交流，将每个邮箱存储在一个平面 XML 文件中(eesh，不是一个非常健壮的数据库存储)

那是不久前的事了。从那以后，我希望我的技能有所提高，我能够更加优雅和成熟地解决问题。

然而，一直有一个项目是我永远无法完成的:写一个计算器。我从来不擅长解析文本。有趣的是，现在我回头看我的方法，事后我可以承认，我在某些方面是对的，而在其他方面却大错特错。事实是，在创建计算器时需要一些深谋远虑。这篇文章将记录我目前对如何解决这个看似简单的任务的理解。我并不声称已经达成了专家共识。

## 问题

让我们从最终目标开始，并从那里向后工作。最后，我想编写一个程序——让它以`calc`命名——我可以像这样调用它:

```
$ calc
Welcome to Calculator 1.0
Please enter an expression on the following line:
> 1 + 2 * -3 + 2^+3^2
=> The answer is 507
> 1 + 2 * (-3 + 2^+3^2)
=> The answer is 1019 
```

Enter fullscreen mode Exit fullscreen mode

嗯，让我们试着从这个理论演示中得出一些规范:

我们需要支持以下内容:

1.  运算符优先级
2.  右结合性(`1^2^3`表示`1^(2^3)`，不是`(1^2)^3`)
3.  有些运算符同时是中缀*和*一元
    *   在表达式`1 - 2`中，`-`是一个中缀运算符
    *   在表达式`-2`中，`-`是一元运算符
4.  使用括号可以覆盖运算符优先级

当我第一次尝试解决这个问题时，4 号似乎是最容易解决的问题。当我第一次尝试编写这个程序时，我第一次通过寻找括号组(通过寻找匹配的括号)，并将这些组拉出到开始看起来像抽象语法树的地方。但最终，在我不得不放弃这个项目去玩猜数字游戏之前，我只能做到这些了。

## 工作，当当当

在我意识到操作符优先解析的问题并不像我想象的那么简单后不久，我发现了一个名为 ANTLR 的出色工具。ANTLR 是一个解析器生成器，它将语法作为输入，并生成代码，将符合语法的文本解析成抽象语法树。这意味着你可以写一个语法，比如:

```
NUM: '\d+';
e:
    NUM
  | '-' e
  | e '^' e <assoc=right>
  | e '*' e
  | e '/' e
  | e '+' e
  | e '-' e
  ; 
```

Enter fullscreen mode Exit fullscreen mode

ANTLR 可以使用类似的语法为您生成一个解析器。这个解析器将获取输入文本，比如说`1 + 2 * 3`，并生成一个抽象语法树，如下所示:

```
 +
 / \
1   *
   / \
  2   3 
```

Enter fullscreen mode Exit fullscreen mode

注意，这个树遵循您在语法中定义的优先规则。

耶！毕竟创建一个解析器非常容易！直到你的好奇心战胜了你，你想知道这个叫做解析器生成器的神奇的东西是如何工作的。此外，尽量不要查看生成的解析器代码:它会变得异常复杂。

一天结束时，我听说越来越多的语言正在使用手工编码的递归解析器，我很好奇。我将如何用自己编写的代码编写计算器应用程序？

## 认识一下我的朋友，沃恩·普拉特

实际上，他并不是我的朋友，但是在学习了他的自顶向下操作符优先解析策略后，我不禁想起了他。在高层次上，Pratt 解析的工作方式是扫描输入令牌，并将它们分为两类:

1.  向右操作的操作符，**没有**向左-上下文
    *   这方面的一个例子是一元负号`-1`
    *   Pratt 称一个操作者如何在没有左上下文的情况下向右消费的规范为“零表示”，或简称为“NUD”
    *   例如，`nud('-')`可以产生一个 AST 节点`Negative(operand=...)`
2.  对两个操作数从左向右操作的运算符
    *   这是任何简单的中缀运算符；例如:`1 + 2`
    *   Pratt 把一个操作者如何用一个左上下文向右消费的规范称为它的“左外延”，或简称为“LED”
    *   例如，`led(left, '-')`可以产生一个 AST 节点`Subtraction(left=left, right=...)`

请注意，在这种情况下，我们是从左向右扫描的。这意味着我们在扫描输入的同时也在形成 AST。例如，让我们简单地解析字符串`1 + 2 + 3`:

我们从第一个标记开始，注意我们看不到其余的标记(这里用下划线“_”:
表示)

```
1 _ _ _ _ 
```

Enter fullscreen mode Exit fullscreen mode

当我们第一次开始时，我们没有左上下文，所以我们取第一个令牌的 NUD:`NUD(1)`。在这个简单的例子中，一个数字的 NUD 就是这个数字本身，所以`1`仍然是一个`1`。让我们继续，看看下一个令牌:

```
1 + _ _ _ 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们有一个左上下文(即`1`)，所以我们将使用`LED(1, '+')`。现在，假设 LED 需要做的就是消耗下一个数字并返回到目前为止的子树。在这种情况下，LED 将产生:

```
 + _ _
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

这个子树就是我们新的“左”。然后我们将弹出下一个操作符:

```
 + + _
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

好的，操作符是“+”，我们有一个左上下文(我们的子树)，所以我们再次取`LED(left, '+')`:

```
 +
   / \
  +   3
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

我们现在在输入的末尾，所以解析停止了，我们有了抽象语法树！到目前为止，我们可以伪代码化我们的过程:

```
function expr() {
    let left = nud(next())
    while (!eof)
        left = led(left, next())
    return left
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，一切顺利。

## 优先虽然...

好吧，我们有个问题。如果我们在字符串`1 + 2 * 3`上执行我们的伪代码，我们将得到下面的抽象语法树:

```
 *
   / \
  +   3
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

这是不正确的:它会在“*”之前计算“+”，所以我们有一个问题。我们需要的是某种编码运算符优先级的方法。事实上，当我们在这里进行解析时，问题就出现了:

```
 1 + _ _ _ 
```

Enter fullscreen mode Exit fullscreen mode

我们不知道乘法即将到来，也不知道我们需要将`2 * 3`解析为它自己的表达式。

嗯，这里有一些我们应该注意的关键词:我们需要将`2 * 3`解析为它自己的表达式。这意味着我们的`expr()`函数需要递归调用。目前，我们是这样定义 LED 的:

```
LED(left, operator) = Tree(left, operator, right=next()) 
```

Enter fullscreen mode Exit fullscreen mode

如果更像这样呢？:

```
LED(left, operator) = Tree(left, operator, right=expr())
                                                 ^^^^ 
```

Enter fullscreen mode Exit fullscreen mode

然而，当我们需要一个操作符的 LED 时，我们不希望调用`expr()`并消耗剩余的输入。我们会希望它在某个时候停止。例如，假设我们正在解析字符串`1 * 2 - 3`，并且我们正处于解析过程的这一点:

```
1 * _ _ _ 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们只希望`expr()`解析下一个数字，而不是整个表达式`2 - 3`。我们需要一种方法来通知`expr()`何时停止。

但是当我们想停下来/跑的时候，模式是什么呢？我给你一个提示:这与运算符优先级有关...

## 权力优先

事实证明，作为人类，我们可以非常直观地选择运算符优先级。我们看一个像`1 + 2 * 3`这样的表达式，我们知道这个表达式*中的`*`将*与 2 和 3 绑定在一起。加号*将*与 1 和子表达式绑定在一起。让我们给它起个名字。让我们给每个经营者一个**约束力**。在这种情况下，`*`的**约束力**大于`+`。事实上，让我们给这个所谓的**约束力**加上一些数字:

*   `+` - `10`
*   `*` - `20`

事实上，这些数字是任意的。重要的是`*`的结合力大于`+`的结合力。但是这对我们有什么帮助呢？当我们在特定的绑定能力下解析一个表达式时，当我们遇到一个绑定能力小于我们当前解析的绑定能力时，我们需要停止。换句话说，当我们遇到较低的约束力时，我们会退出，不再追究。

让我们把这个措辞放到我们的伪代码中(`rbp`代表“正确的绑定能力”):

```
function expr(rbp = 0) {
    let left = nud(next())
    while (bp(peek()) > rbp)
        left = led(left, next())
    return left
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是我们最终版本的`expr()`。这就是 Pratt 解析器的基础。最后，我们必须提供所有的约束力，并为每个运营商定义 NUD/LED。

这意味着我们需要从上面重新定义 LED。而之前是:

```
LED(left, operator) = Tree(left, operator, right=expr()) 
```

Enter fullscreen mode Exit fullscreen mode

现在需要的是:

```
LED(left, operator) = Tree(left, operator, right=expr(bp(operator)))
                                                      ^^ 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过例子来了解这是如何工作的。让我们解析`1 * 2 + 3` :

```
// call expr(rbp = 0)
1 _ _ _ _
// take NUD(1)
1 * _ _ _
// take LED(1, '*') = Tree(1, '*', right=expr(bp('*')))
//                  = Tree(1, '*', right=expr(20))
  * _ _ _
 / \
1   expr(20)

// start recursion: expr(20)
// take NUD(2)
2 + _
// 20 < bp('+') ?
// nope, return left so far (2)

// resume previous recursion
  * + _
 / \
1   2
// 0 (current rbp) < bp('+')?
// yes! continue parsing...
// take LED(left, '+') = Tree(left, '+', right=expr(bp('+')))
//                     = Tree(left, '+', right=expr(10))
    +
   / \
  *   expr(10)
 / \
1   2

// start recursion: expr(10)
// abbreviated: will return 3

// resume previous recursion
    +
   / \
  *   3
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

希望这不会太难理解。每当我完成这个过程时，我的心都会弯曲。但是我们完了！注意操作符的优先级是如何正确地保留在生成的抽象语法树中的。

## 不是迂腐，而是如何处理右结合性？

事实证明这几乎太容易了。假设我们希望我们的取幂操作符是右关联的(这是应该的)。如果我们像实现其他中缀操作符一样实现`LED(left, '^')`，例如:

```
LED(left, '^') = Tree(left, '^', expr(bp('^'))) 
```

Enter fullscreen mode Exit fullscreen mode

那么当我们碰到一个较小的(或相等的)算符时，正确的约束力就会把我们踢出去！)的约束力，包括我们自己的，这意味着我们将把`1^2^3`解析为:

```
 ^
   / \
  ^   3
 / \
1   2 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们将 LED 的定义调整为:

```
LED(left, '^') = Tree(left, '^', expr(bp('^') - 1))
                                              ^^^ 
```

Enter fullscreen mode Exit fullscreen mode

现在传入`expr(...)`的右约束力将小于'^'.的约束力这将产生正确的树:

```
 ^
   / \
  1   ^
     / \
    2   3 
```

Enter fullscreen mode Exit fullscreen mode

等等，右结合律这么简单？？

## 结论

Pratt 解析器棒极了，一旦你理解了引擎盖下发生的事情，它们真的很简单。事实证明，我现在的一个副业是一个解析 JavaScript 的解析器。有了普拉特的解析，即使解析 JSX 也不会太差。如果你想看我完成的 Pratt-parser-calculator(终于，在这么多年后)，那么就跳到这个项目的 GitHub 上。感谢阅读！