# 附带说一句

> 原文：<https://dev.to/yechielk/parenthetically-speaking-1b>

我在过去的[博客里写过](https://blog.yechiel.me/get-your-fizz-buzz-on-543f2a327a9d)关于[在面试中被问到的有趣问题](https://blog.yechiel.me/welcome-to-the-mvc-restaurant-fb1709047914)以及[如何回答这些问题](https://blog.yechiel.me/welcome-to-the-mvc-restaurant-fb1709047914)。

在这篇文章中，我想讨论一个流行的面试问题，以及如何回答这个问题。我想谈论这个具体问题的原因是，用来解决这个问题的算法与人类处理相同问题的方式非常相似。

#### 提问

问题是这样的:

假设您有一个很长的文本字符串，有许多括号，所有的括号都嵌套在一起(例如一个很长的 LISP 程序)。您需要确保句子的格式正确，并且每个左括号都有相应的右括号。

在我用代码编写解决方案之前，让我们想一想，作为人类，我们将如何处理这个问题。

一个合理的方法是计算所有的左括号和右括号，并确保数字是相同的。

让我们用 JavaScript 试试:

```
function parenthesesChecker(text) {

  var open = 0
  var close = 0

  for(i = 0; i < text.length; i ++) {
    if(text[i] === "(") {
      open += 1
    } else if(text[i] === ")") {
      close += 1
    }
  }

  return open === close
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地检查代码。

我们做的第一件事是，声明两个名为`open`和`close`的变量，并将它们的值设置为 0。

然后我们对文本进行迭代，对于找到的每个左括号，我们将`open`的值增加 1，对于每个右括号，我们将`close`的值增加 1。

最后，我们检查`open`和`close`的值是否相同。如果他们是我们返回`true`，否则我们返回`false`。

简单。

#### 有用吗？

让我们对照一些测试字符串来检查算法，以确保它能够工作。

先说最简单的:`"()"`

我们有一个左括号和一个右括号，它们返回`true`。完美。

`"()()()"`也返回`true`。

我们来试试一些嵌套括号:`"((()))"`、`"(()())"`、`"(()(()))"`都返回`true`。

现在让我们看看一些不好的字符串，看看它们是否都返回`false` : `"("`，`"(()"`，`"(())()(("`都返回`false`，就像它们应该的那样。

但是我们遇到了一些问题:`")("` `"(()))("`返回`true`，尽管它们的格式很差。左括号和右括号的数量相等，但它们要么以右括号开始，要么以左括号结束。

我们可以添加一个 if 语句来检查第一个和最后一个括号，但是这种情况怎么办:`"())(()"`？第一个和最后一个括号是好的，有相同数量的左括号和右括号，但它仍然是一个坏的字符串。

#### 我们需要一种新的方法。

让我们再想一想，作为人类，我们会如何看待这个问题？一个更好的方法是，不要计算开括号和闭括号，而是记录有多少括号保持打开。我们来看这个句子，每当我们看到一个左括号，我们就说“这是一个左括号”，“这是两个左括号”，等等。每当我们到达一个右括号，我们就开始倒数:“这是一个开”，“没有开”，等等。如果我们曾经低于零，我们知道我们有麻烦了，如果我们到达最后括号仍然打开，我们知道我们也有麻烦了。

在 JavaScript 中会是什么样子呢？非常类似于我们上一个函数:

```
function parenthesesChecker(text) {

var open = 0

for(i = 0; i < text.length; i ++) {
    if(text[i] === "(") {
      open += 1
    } else if(text[i] === ")") {
      open -= 1
    }
    if(open < 0)
      return false
  }

  return open === 0
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是，我们只跟踪一个名为`open`的变量，而不是跟踪两个变量`open`和`close`。当我们到达左括号时，我们增加我们的`open`变量，当我们到达右括号时，我们减少它。如果`open`的值低于 0，我们自动返回`false`，如果最后不是 0，我们也返回`false`。

这种解决方案似乎适用于所有情况。如果我错过了什么，我相信你会在评论里指出来的。

* * *

这篇文章是从我的博客[代码 Hassid](https://blog.yechiel.me/parenthetically-speaking-a275f86c833e) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)