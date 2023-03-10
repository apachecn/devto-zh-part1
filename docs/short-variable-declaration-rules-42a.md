# 短变量声明规则

> 原文：<https://dev.to/inancx/short-variable-declaration-rules-42a>

这是我的主帖*Visual how guide to Go variables*、[Go check out](https://blog.learngoprogramming.com/learn-go-lang-variables-visual-tutorial-and-ebook-9a061d29babe?source=collection_home---4------0----------------)的衍生帖。

* * *

## ðÿ'‰你不能用它来声明包级变量。

```
illegal := 42
func foo() {
  legal := 42
} 
```

Enter fullscreen mode Exit fullscreen mode

## ðÿ'‰你不能用它两次:

```
legal := 42
legal := 42 // <-- error 
```

Enter fullscreen mode Exit fullscreen mode

因为，:=引入了“一个新变量”，因此使用它两次并不重新声明第二个变量，所以它是非法的。

## ðÿ'‰如果其中一个变量是新的，你可以在“多变量”声明中使用它们两次:

```
foo, bar  := someFunc()
foo, jazz := someFunc()  // <-- jazz is new
baz, foo  := someFunc()  // <-- baz is new 
```

Enter fullscreen mode Exit fullscreen mode

这是合法的，因为，你没有重新声明变量，你只是用一些新的变量给现有的变量重新赋值。

## ðÿ'‰如果一个变量之前已经声明了相同的名字，你可以使用它们:

```
var foo int = 34
func some() {
  // because foo here is scoped to some func
  foo := 42  // <-- legal
  foo = 314  // <-- legal
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，`foo := 42`是合法的，因为，它在`some() func`的范围内重新声明了`foo`。`foo = 314`是合法的，因为，它只是给`foo`重新分配了一个新的值。

## ðÿ'‰你可以用它们进行多变量的声明和赋值:

```
foo, bar   := 42, 314
jazz, bazz := 22, 7 
```

Enter fullscreen mode Exit fullscreen mode

## ðÿ'‰您可以在限定了作用域的语句上下文中重用它们，如 if、for、switch:

```
foo := 42

if foo := someFunc(); foo == 314 {
  // foo is scoped to 314 here
  // ...
}

// foo is still 42 here 
```

Enter fullscreen mode Exit fullscreen mode

因为，`if foo := ...`赋值，只能由 if 子句访问。

* * *

在 [twitter](http://twitter.com/intent/tweet) 上分享这个。请分享爱。
ðÿ我主要是在推特上发布关于围棋的消息，你可以在推特上关注我:@ inancgumus。
ðÿ“我还在创建一个教授围棋的在线课程。[加入我的简讯](http://eepurl.com/c4DMNX)！
*你会收到我的新帖子和即将推出的在线围棋课程的通知(每周三)*。

* * *

*原载于[围棋短变量声明规则](https://blog.learngoprogramming.com/golang-short-variable-declaration-rules-6df88c881ee)于[学习围棋编程](https://blog.learngoprogramming.com)。*