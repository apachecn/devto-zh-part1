# Go 中的包

> 原文：<https://dev.to/codehakase/packages-in-go-4mf>

> 你好。如果你一直在关注这个系列，那你就太棒了！！！。

在上一篇文章中，我们研究了 Go 中的函数，以及如何定义它们。在这一部分，我们要看看包装。

## 什么是套餐？

包用于组织源代码，以获得更好的可重用性和可读性。包使维护 Go 应用程序变得容易。

到目前为止，在前面的文章中，我们已经看到了只有一个主函数和其他函数文件的程序。将所有的源代码都写在一个文件中不是一个好方法，因为维护和重用代码变得非常困难。

##主包和主函数
每一个可执行的 Go 程序，都必须包含一个`main`函数。`main`函数作为执行的入口点。

> 主函数应该驻留在主包中。

包在源文件中被定义为`package packageName`，它位于源文件的第一行。

### 进口

`import "packagename"`语句用于将现有的包导入到 Go 源中。当导入一个包时，您可以访问该包中定义的方法:

```
package main

import "fmt"

func main() {
  fmt.Println("Hello Golang Packages!!!")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义套餐

让我们创建一个自定义包`geometry`一个具有最少几何计算的包。

> 注意:属于一个包的源文件应该放在它们自己单独的文件夹中。Go 中的一个惯例是用包的相同名称来命名这个文件夹。

让我们为我们的包创建目录结构，

```
$ cd $HOME/gocode/src
$ mkdir geometry 
```

Enter fullscreen mode Exit fullscreen mode

我们将用以下内容创建一个新文件`rectangle/rectangle.go`:

```
package rectangle

import "math"

func Diagonal(len, wid float64) float64 {  
    diagonal := math.Sqrt((len * len) + (wid * wid))
    return diagonal
}

func Area(len, wid float64) float64 {  
    area := len * wid
    return area
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们创建了两个函数来计算矩形的面积和对角线。长方形的面积是长和宽的乘积。矩形的对角线是长和宽的平方和的平方根。`math`包中的`Sqrt`函数用于计算平方根。

## 导入定制包

要使用自定义软件包，我们必须首先导入它。我们必须根据工作区内的`src`文件夹指定定制包的路径。

让我们创建我们的入口文件，`geometry.go`，它将包含我们的包的主要函数。

```
 package main

import (
  "fmt"
  "geometry/rectangle"
)

func main() {
    var rectLen, rectWidth float64 = 6, 7
    fmt.Println("Geometrical shape properties")
        /*Area function of rectangle package used
        */
    fmt.Printf("area of rectangle %.2f\n", rectangle.Area(rectLen, rectWidth))
        /*Diagonal function of rectangle package used
        */
    fmt.Printf("diagonal of the rectangle %.2f ",rectangle.Diagonal(rectLen, rectWidth))
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码导入了矩形包，并使用它的面积和对角线函数来求矩形的面积和对角线。Printf 中的`%.2f`格式说明符是将浮点截断到两位小数。应用程序的输出是:

```
Geometrical shape properties  
area of rectangle 42.00  
diagonal of the rectangle 9.22 
```

Enter fullscreen mode Exit fullscreen mode

## 导出名称

我们将矩形包中的函数`Area`和`Diagonal`大写。这在围棋中有着特殊的意义。任何以大写字母开头的变量或函数都是导出名称。只有导出的函数和变量可以从其他包中访问。在这种情况下，我们需要从主包中访问面积和对角线函数。因此它们是大写的。

## 初始化功能

每个包都可以包含一个`init`函数。init 函数不应该有任何返回类型，也不应该有任何参数。init 函数不能在我们的源代码中显式调用。init 函数如下图所示

```
func init() {  
} 
```

Enter fullscreen mode Exit fullscreen mode

init 函数可用于执行初始化任务，也可用于在执行开始前验证程序的正确性。

包的初始化顺序如下

*   首先初始化包级变量
*   接下来调用 init 函数。一个包可以有多个 init 函数(或者在一个文件中，或者分布在多个文件中),它们是按照出现在编译器中的顺序被调用的。

如果一个包导入其他包，导入的包将首先被初始化。

包裹到此为止。包帮助我们以一种非常简洁的方式组织代码，为我们的程序创建可以分发的实用程序。

如果我错过了什么，请在评论中告诉我。