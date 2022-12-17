# Go 中的常数。

> 原文：<https://dev.to/codehakase/constants-in-go-92o>

在这一部分，我们将看看 Go 中的常量，以及它们如何在程序中发挥作用。

*   常量像变量一样被声明，但是使用了`const`关键字。

*   常量可以是字符、字符串、布尔值或数值。

*   不能使用:=语法声明常量。

考虑下面的代码:

```
...

const MAX_WT = 200

func main() {

  fmt.Println("Max wait time: ", MAX_WT)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，常量`MAX_WT`被定义为值 200，并且在整个脚本中都可用。

常量的值在编译时应该是已知的。因此，它不能赋给函数调用返回的值，因为函数调用发生在运行时。

## 字符串常量

任何用双引号括起来的值(例如“John Doe”)，在 Go 中都是一个字符串常量。

默认情况下，字符串常量是非类型化的:

```
 const name = "James Bond" 
```

Enter fullscreen mode Exit fullscreen mode

既然 Go 是强类型的，而且所有的变量都需要显式类型，但是上面的代码工作正常，为什么呢？

> 常数有一个与之相关联的默认类型，当且仅当一行代码需要时，它们才提供默认类型。

#### 键入字符串常量

可以创建类型化常量吗？是的，这里有一个例子:

```
const name string = "James Bond" 
```

Enter fullscreen mode Exit fullscreen mode

## 布尔常数

布尔常量是两个无类型常量 true 和 false。字符串常量的相同规则也适用于布尔值。这里有一个定义布尔常量的例子:

```
const published = true 
```

Enter fullscreen mode Exit fullscreen mode

## 数字常量

数字常量包括整数、浮点数和复数常量。数字常量中有一些微妙之处。

```
package main

import "fmt"

func main() {  
    const a = 5
    var intVar int = a
    var int32Var int32 = a
    var float64Var float64 = a
    var complex64Var complex64 = a
    fmt.Println("intVar",intVar, "\nint32Var", int32Var, "\nfloat64Var", float64Var, "\ncomplex64Var",complex64Var)
} 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的代码，您应该会看到下面的输出:

```
$ go run main.go

intVar 5 
int32Var 5 
float64Var 5 
complex64Var (5+0i) 
```

Enter fullscreen mode Exit fullscreen mode

常数就是这样。以下是你需要记住的几点:

*   常数只能定义一次
*   常数可以在没有类型的情况下定义
*   常数可以在定义它的范围内使用

在下一部分，我们将看看 Go 中的函数。

有什么反馈吗？请在评论中告诉我。

干杯！