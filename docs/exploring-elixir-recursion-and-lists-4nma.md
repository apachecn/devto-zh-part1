# 探索灵药递归和列表

> 原文：<https://dev.to/allanmacgregor/exploring-elixir-recursion-and-lists-4nma>

最近一直在玩一种新的语言叫做[仙丹](https://elixir-lang.org/)。Elixir 是一种**函数式编程语言**，专门设计用于创建可伸缩和可维护的应用程序。

和许多开发人员一样，我在编程语言和范例方面的主要经验来自于面向对象编程。函数式编程是一个完全不同的领域，随着我探索和学习的深入，我越来越发现函数式编程背后的承诺和想法。

一直困扰我的一件事是函数式编程严重依赖递归。现在，从技术上讲，elixir 不是一种纯粹的功能性语言，他们实现**不变性**的方式有点不同；也就是说，elxir 和其他函数式编程语言一样**没有循环**。

没有循环！？没有 **foreach** ，没有 **for** ，没有**而**我们反而得到递归；其中函数被递归调用，直到达到阻止递归动作再次发生的条件——例如处理列表中的每个元素，直到列表为空。

听起来很困惑，对吧？让我们看一个例子:

前面的代码是我为[exercisem . io](https://exercism.io)所做练习的一部分，该练习要求重新实现本地列表模块的功能；count 函数将获取一个数字列表，并返回列表中元素的计数。

如果这是你第一次看长生不老药，你首先会想到的是，我们似乎是在两次定义计数函数；这不是一个错误，这是 elixir 语言最令人惊讶的特性之一，它被称为**模式匹配**我不会在这篇文章中详细介绍它是如何工作的，但可以说，只有当我们收到一个空列表时，才会调用第一个定义。

回到递归，我们来看看函数的第二个定义:

```
 def count([_|tail]) do
    count(tail) + 1
  end 
```

Enter fullscreen mode Exit fullscreen mode

我们在这一点上所做的就是取出列表的第一个元素，将其传递给 count 函数，并在每个实例上返回**结果+ 1** ，但是这到底是如何工作的呢？让我们假设我们将**【1，2，3，4，5】**作为参数:

```
count([1,2,3,4,5]) #returns 4 + 1
  "this in turn calls" -> count([2,3,4,5]) #returns 3 + 1 
  "this in turn calls" -> count([3,4,5]) #returns 2 + 1
  "this in turn calls" -> count([4,5]) #returns 1 + 1
  "this in turn calls" -> count([5]) #returns 0 + 1
  "this in turn calls" -> count([]) #returns 0 
```

Enter fullscreen mode Exit fullscreen mode

这是递归能力的一个非常简单的例子，但是很好地说明了这个概念。目前，递归最重要的一点是，在遍历一个值列表时，我们不需要可变状态——也就是解决问题。