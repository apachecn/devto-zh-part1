# 2018 年 Lisp 课程清单

> 原文：<https://dev.to/lambdude/a-list-of-lessons-from-lisp-for-2018-24f5>

我 2017 年的一个决心是学习 Lisp。我最近学了哈斯克尔和长生不老药。Lisp 之所以吸引人，是因为它是另一种类型的函数式语言。

学习 Lisp 对我今年剩下的时间很有帮助，以至于我决定与他人分享这一经验，这样他们可能会将 Lisp 添加到他们 2018 年的决心列表中。

学习 Lisp 的价值在于想法。我不写任何 Lisp 代码。但是我一直在我的代码中使用 Lisp 的思想。因此，在新的一年里，我整理了一份学习 Lisp 的清单。我希望这能激励其他人在 2018 年学习 Lisp！

## 第一课:名字很重要

标签承载着意义。标签是强大的。当你给某物贴上标签时，你就获得了对它的控制力。也就是说，你获得了谈论 it 的权力。这是抽象的概念。当我们创建一个类时，我们是在创建一个抽象来讨论系统的某个部分。但是这并不是抽象停止的地方。

当你创建一个函数时，你也在创建一个抽象。在 Java 中，类是抽象的主要方法。在 Lisp 中，抽象的主要方法是使用函数。这是函数式语言的标志之一。

## 第二课:函数也是对象

在许多编程语言中，函数是一等公民。这意味着函数是一种类型。它们可以是函数的输入，也可以是函数的输出。如果你的语言支持匿名函数，会有额外的好处，很多人现在都支持匿名函数。

Lisp 具有一等公民的功能。Lisp 有原始的匿名函数，叫做 lambda。lambda 实际上和闭包是一回事，闭包是一个匿名函数，可以捕获它的环境。

当您使用更高阶的函数，如贴图、过滤或折叠时，这变得特别强大。lambda 是如此强大，以至于整个 Lisp 解释器可以只用 lambda 来实现。甚至是数据结构！

## 第三课:递归不一定是可怕的

递归对许多开发人员来说是可怕的。出现这种情况有几个原因。可能会担心产生无限循环。也许你不喜欢用事物本身来定义它。但是没什么好害怕的，你会发现，只要满足某些先决条件，用事物本身来定义它们听起来很自然。

递归由四部分组成:一个基本情况，一个递归情况，一个条件和问题的简化。让我们为此写一些伪代码:

```
function factorial(n)
  if n is less than or equal to 0 # condition
    1 # base case
  otherwise
    n * factorial(n - 1) # recursive case, reduction of the problem 
```

Enter fullscreen mode Exit fullscreen mode

事实上，Lisp 代码并没有什么不同:

```
(define (factorial n)
  (if (<= n 0)
      1
      (* n (factorial (- n 1))))) 
```

Enter fullscreen mode Exit fullscreen mode

这种情况决定了我们的功能是否会停止。一个总是假的条件会变成一个无限循环。在这里，只要我们的`n`是一个正整数，我们的条件在某一点上保证为真。基本情况是如果条件为真会发生什么。我们的基础案例是 1，这是对`factorial(0)`的回答。递归情况是条件为假时发生的情况。递归的情况应该包括问题本身的简化。这里，`factorial(n)`变成了子问题`n * factorial(n - 1)`。这里的还原非常重要。如果我们不从`n`中减去一，那么我们的条件永远不会成立。这又会导致无限循环。

因此，为了防止递归函数永远运行，您需要一个基于输入的初始值和归约方法保证为真的条件。

你可以写一个 length 函数来减少字符串中的每个字符，直到它变空。你可以写一个 sum 函数来减少数组中的每一项，直到数组变空。这些都是使用相同的递归机制，只是减少的方法不同。

## 第四课:保持数据不变

保持数据不变使您可以并行使用代码，比依赖可变值要少得多的顾虑。如果值在另一个线程中的循环读取它之前改变了怎么办？这些问题随着不变性而消失。但是，很自然的，随之而来的问题是:*“如果我们不能改变价值观，那么我们如何改变价值观？”*

答案是使用旧值构造一个新值，而不是改变旧值。这就是为什么`map`返回一个新列表，而不是一个修改过的列表。

## 第五课:限制范围

Lisp 不鼓励使用全局变量。范围受`let`函数的限制。这也允许我们在特定的上下文中改变值。保持数据在总体上不可变，并在必要时在一定范围内变化，这将有助于保持数据的一致性。

## 第六课:域中的代码

在许多语言中，您直接使用语言的构造进行编码。但是在 Lisp 中，我们定义了一种特定于领域的语言。然后，我们用自己定义的语言编写程序。这使得我们的代码更清晰，更容易理解。这使得错误不太可能进入我们的代码，并且在它们出现时更容易修复。

## 结论

希望你 2018 年考虑学习 Lisp。这是一门非常值得学习的语言。你可能不会用它来直接构建任何东西，但是你会用你从中学到的思想来构建更好的程序。Lisp 挑战你以新的方式思考，拓展你的视野。不管你用什么语言编码，你最重要的技能是解决问题。为什么不在你的工具箱里添加更多的工具呢？