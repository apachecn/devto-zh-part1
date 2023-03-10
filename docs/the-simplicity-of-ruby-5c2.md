# Ruby 的简单性

> 原文：<https://dev.to/tiffany/the-simplicity-of-ruby-5c2>

当我第一次开始[这个博客](https://tiffanywhite.tech)的时候，我正在学习 Ruby，我喜欢它。

这是在开始 freeCodeCamp 或者 Zed Shaw 的艰难学习 Python 系列 <sup id="fnref-2681-1">[1](#fn-2681-1)</sup> 之前。

当我决定以编程为生时，我找到了一些聚会，并在 2014 年参加了我的第一次聚会， [Code & Supply](https://codeandsupply.co/) 那里的主题是 Ruby 在我们的系统上设置它，并编写一个非常简单的 Ruby 程序。

鲁比是如此简单。以此为例:

```
class MyClass
  def initialize(name, age)
    @name = name
    @age = age
  end
end 
```

如果你对 OOP 有所了解，你就会知道这里发生了什么。

Ruby 的美妙之处在于其极其易读的语法。

```
class MyClass 
```

👆创建一个类，我们将在其中保存所有的对象和方法。

`def`:定义。这里我们用两个局部变量定义一个方法`initialize`:`name`和`age`。我们将它们分配给实例变量`@name`和`@age`，以便在对象范围之外使用它们*。我们用关键字`end`结束方法和类。*

在 OOP 实例变量语法之外，这非常容易阅读和理解。

## 第一天的感觉

现在是晚上 10 点，我真的需要早点睡觉。但我只是想有机会写一写 dev.to 的团队在我学习的过程中是如何耐心地陪着我。

入职轻而易举，我已经开始挖掘代码库。

我的第一天充满了紧张和对自己的期望 <sup id="fnref-2681-2">[2](#fn-2681-2)</sup> 。

记住我不是一个项目的唯一开发人员，我有一个团队需要参考，这需要一些时间来适应。这个团队很棒，我肯定会问问题:从来不会对此感到害羞。我只是太害羞和安静了。这是我正在努力的事情。

到目前为止，这是一个很棒的第一周，我期待着接下来的几周。

* * *

1.  LPTHW 相当固执己见，不确定是否还推荐给新开发者。↩
2.  睡眠很少。试图解决这个问题。↩