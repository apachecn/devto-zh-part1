# Go 变量入门

> 原文：<https://dev.to/codehakase/getting-started-with-go---variables-aef>

在上一篇文章- [Golang - Getting Started](https://dev.to/codehakase/golang---getting-started-16c) 中，我介绍了 Go 编程语言，以及如何为你的平台设置开发环境。在本文中，我们将讨论围棋中的变量。

## 什么是变量？

变量是为存储特定类型值的内存位置指定的名称。在 Go 中，有各种不同的语法来声明变量:

### 单变量声明

```
package main

import "fmt"

func main() {
  var bsLine string = "Man's not hot!"

  fmt.Println(bsLine)
} 
```

Enter fullscreen mode Exit fullscreen mode

`var bsLine string`声明了一个名为`bsLine`的`string`类型的变量。我们没有给变量赋值。我们给这个变量分配了一个字符串值 *Man's not hot！*。如果变量声明没有赋值，go 会自动用变量类型的零值来初始化它。如果您运行这个程序，您会看到下面的输出:

```
$ cd $HOME/go/getting-started

$ go run variables.go

Man's not hot! 
```

Enter fullscreen mode Exit fullscreen mode

### 变量类型推断

如果一个变量有一个初始值，Go 将会自动使用这个初始值来推断这个变量的类型。因此，如果一个变量有一个初始值，变量声明中的类型可以省略。

```
package main 

import "fmt"

func main() {

  var tip = 300 // type inference happening here!

  fmt.Println("Let's tip him $", tip) // Let's tip him $300
} 
```

Enter fullscreen mode Exit fullscreen mode

### 多变量赋值

在 Go 中，可以在一条语句中声明多个变量或者给多个变量赋值:

```
package main

import "fmt"

func main() {

  var day, month, year int = 19, 11, 2017

  fmt.Printf("This article was posted on %d-%d-%d", day, month, year) // This article was posted on 19-11-2017
} 
```

Enter fullscreen mode Exit fullscreen mode

### 不同类型的多重作业，可能吗？

是的，它是。有一种不同的语法来实现这一点:

```
 package main

import "fmt"

func main() {

  var (
    name string = "Francis Sunday"
    alias string = "codehakase" 
  )

  fmt.Printf("Hi I'm %s, in the tech world I'm referred to as %s", name, alias)
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会打印`Hi I'm Francis, in the tech world I'm referred to as codehakase`。

### 有缺人手的吗？

是的，有一种更短的方法来声明变量，通过使用特殊的赋值操作符`:=`

```
package main

import "fmt"

func main() {

  publishDate := "19/11/2017"

  fmt.Println("Date: ", publishDate) // Date: 19/11/2017
} 
```

Enter fullscreen mode Exit fullscreen mode

## *逮到你了*

### 声明非新变量

```
 package main

import "fmt"

func main() {

  name, gender := "Francis", "male"

  fmt.Printf("Name: %d, Gender: %d", name, gender)

  gender := "female"

  fmt.Println("New gender: ", gender) //
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码声明并赋值给两个变量`name`和`gender`，并在其下面打印一个格式化的字符串。这个程序会在第一条打印语句后立即**死机**。它将抛出错误`no new variables on left side of :=`，因为这两个变量都已经被声明，并且在`:=`的左侧没有新的变量。因此，如果我们需要改变`gender`的值，我们将使用等于运算符`=`而不是`:=`。

```
...

gender = "female" 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

嘿！到目前为止，你已经做了很好的工作，希望你现在理解了变量是如何在 Go 中定义的，以及如何编写简单的程序将数据存储在变量中。在本系列的下一篇文章中，我将会介绍 Go 中的**类型**，敬请关注。

如果你错过了上一篇文章，请点击这里查看。

我肯定我可能错过了什么，帮我个忙，在 XD 下面的评论区举报这个家伙。

*你可以查看我的[技术博客](https://hakaselabs.github.io)，在 Twitter 上关注我 [@codehakase](https://twitter.com/codehakase)*