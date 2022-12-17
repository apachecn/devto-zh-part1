# Go 中的函数。

> 原文：<https://dev.to/codehakase/functions-in-go-3dd>

在这个系列的这一部分，我们将看看函数，以及它们在围棋中是如何工作的。

## 什么是函数？

函数是执行特定任务的代码块。函数接受输入，对输入执行一些计算，然后生成输出。

在 Go 中有两种类型的函数可以使用。1)用户定义的和 2)标准的功能。

### 用户自定义函数

这种类型的函数，我们创建自己来执行程序中的特定任务。

#### 申报

下面是声明函数的语法:

```
...

func sayHello() {

  fmt.Println("Hello World!")  
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例代码中，我们声明了一个函数`sayHello`，当调用该函数时，它打印字符串`Hello World`。Go 中的每个函数都以`func`关键字开始，后面是函数名(我们将在后面的文章中讨论命名约定)，括号，然后是一个用花括号括起来的块(`{ }`)。

```
...

func greet(name string) {

  fmt.Println("Hello " + name)
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子是另一个函数，这次它接受一个参数。将参数视为进一步定义函数的信息片段。在上面的代码中，我们声明了一个函数`greet`，它接受 string 类型的参数`name`。调用这个函数简单明了:

```
...

func main() {

 greet("Francis Sunday")
}

func greet(name string) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

> PS:使用参数时，需要显式定义其类型。

```
 package main 

import "fmt"

func split(number int) (int, int) {

  x := number * 4 / 9
  y := number - x

  return
}

func main() {

  x, y := split(100)

  fmt.Println("%d + %d = 100", x, y)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们声明了一个函数`split`，它接受一个整数作为参数`number`。该函数接受一个类型为`int`的参数，并返回两个类型为`int`的数字，这两个数字加起来就是输入参数。

运行该程序，您应该得到以下内容:

```
$ go run functions.go

44.44 + 55.56 = 100 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在本文中，我们研究了 Go 中的函数，它们如何帮助我们将重复的任务组织成更小的代码块，我们可以在程序中的任何地方重用这些代码块。在本系列的后续文章中，我们将探讨更多关于函数的内容。

如果我错过了一些重要的东西，请随时发表评论。