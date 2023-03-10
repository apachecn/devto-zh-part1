# swift 中的“let”关键字

> 原文：<https://dev.to/abelagoi/the-let-keyword-inswift-2ha5>

在 swift 中，我们使用`let`关键字来声明一个常量变量，常量是一个变量，一旦声明，其值就不能改变。

### 声明常数

我们可以像下面这样在 swift 中声明一个变量:

```
let myAge: Int = 27 
```

Enter fullscreen mode Exit fullscreen mode

注意上面的关键字`let`，我们用它来表示`myAge`是一个常量，应该保存整数`27`。与使用`var`关键字声明的变量不同，一旦常量被赋值，我们就不能改变它的值。

常数与变量非常相似，除了它的值在声明后不能改变。

### 总结

记住以下几点很重要。你可以用关键字`let`声明一个常量，常量和变量(var)有相同的属性，包括类型注释、命名约定等。，除非一旦分配了值，否则不能更改该值