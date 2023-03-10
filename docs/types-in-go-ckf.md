# Go 中的类型

> 原文：<https://dev.to/codehakase/types-in-go-ckf>

在[之前的文章](https://dev.to/codehakase/getting-started-with-go---variables-aef)中，我写了关于变量以及它们在 Go 中的用法。在这一部分，我们将看看围棋中可用的基本类型。

Go 是一种静态类型语言。这意味着变量总是有特定的类型，并且这种类型不能改变。静态类型一开始可能看起来很麻烦。你将花费大量的时间试图修改你的程序，使它最终能够编译。

以下是中可用的基本类型

*   弯曲件
*   线
*   数字类型
    *   int8，int16，int32，int64，int
    *   uint8, uint16, uint32, uint64, uint
    *   浮动 32，浮动 64
    *   复杂 64，复杂 128
    *   字节
    *   古代北欧文字

### Bool

布尔值是一种特殊的 1 位整数类型，用于表示真和假(或开和关)。布尔值使用三种逻辑运算符:

*   && -还有
*   || -或者
*   ！-不是

下面是一个展示如何使用它们的示例程序:

```
package main

import "fmt"

func main() {
  fmt.Println(true && true)
  fmt.Println(true && false)
  fmt.Println(true || true)
  fmt.Println(!false)
} 
```

Enter fullscreen mode Exit fullscreen mode

运行这个程序应该会给你:

```
$ go run main.go
true
false
true
true 
```

Enter fullscreen mode Exit fullscreen mode

### 字符串

字符串是用于表示文本的具有确定长度的字符序列。Go 字符串由单独的字节组成，通常每个字符一个字节。

```
package main

import "fmt"

func main() {

  fmt.Println("Hello World \n")

  fmt.Println("Golang rocks!")
} 
```

Enter fullscreen mode Exit fullscreen mode

字符串可以用双引号“Hello World”创建，或者用换行符替换反斜线`Hello World`. The difference between these is that double quoted strings cannot contain newlines and they allow special escape sequences. For example `\n`，用制表符替换`\t`。``

 ``### 整数

整数和它们的数学对应物一样，是没有小数部分的数字。(-3, -2, -1, 0, 1, …).与我们用来表示数字的十进制系统不同，计算机使用的是二进制系统。

```
package main 

import "fmt"

func main() {

  fmt.Println("10 + 7 = ", 10 + 7)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run main.go
10 + 7 = 17 
```

Enter fullscreen mode Exit fullscreen mode

### 浮点数

Go 有两种浮点类型: **float32** 和 **float64** (也经常分别被称为单精度和双精度)以及另外两种用于表示复数(具有虚部的数)的类型: **complex64** 和 **complex128** 。

下面是一个简单的程序来说明整数和浮点类型。

```
package main

import (  
    "fmt"
)

func main() {  
    x, y := 2.12, 5.4

    sum := x + y
    diff := x - y

    fmt.Println("the sum: ", sum, "diff: ", diff)
} 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的程序，您应该看到以下内容:

```
$ go run main.go
the sum:  7.5200000000000005  diff:  -3.2800000000000002 
```

Enter fullscreen mode Exit fullscreen mode

### 复数

**复数 64:** 有 32 个实数和虚数部分的复数
**复数 128:** 有 64 个实数和虚数部分的复数

内置函数 complex 用于构造具有实部和虚部的复数。

> 它以实部和虚部作为参数，并返回一个复杂类型。实部和虚部应该是同一类型。即浮动 32 或浮动 64。如果实部和虚部都是 float32，则该函数返回类型为 *complex64* 的复数值。如果实部和虚部都是 float64 类型，则该函数返回类型为 *complex128* 的复数值。

```
package main

import "fmt"

func main() {
  c1 := complex(2, 3)
  c2 := 2 + 47i

  cadd := c1 + c2

  fmt.Println("sum:", cadd)

  cmul := c1 * c2
  fmt.Println("product:", cmul)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run main.go
sum: (4+50i)
product: (-137+100i) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这一部分，我们看到了围棋的基本类型。在本系列的下一部分中，我们将交替使用这些不同的类型。在下一部分，我们将看看 Go 中的常量。

我可能错过了什么，帮我个忙，在下面的评论中举报我，或者发推文给我。

PS:这是该系列的第三部分，你可以参考第一部分来获得该系列的提示。

干杯！``