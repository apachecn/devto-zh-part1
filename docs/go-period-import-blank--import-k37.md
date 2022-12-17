# 去言語のperiod(.)importとblank(_) importについて

> 原文：<https://dev.to/shiena/go-period-import-blank--import-k37>

# 关于输入

我们调查了 Go 语言的 period import 和 blank import。
import 整体规格可以从以下内容确认。

*   [Go 编程语言规格/导入宣言](http://golang.jp/go_spec#Import_declarations)
*   [Go 编程语言规范/导入声明](http://golang.org/ref/spec#Import_declarations)

## 句号(。)导入

引自 Go 编程语言规范导入声明

> 如果指定句点(.)而不是名称，则在导入源文件的文件块中声明导入包的包块中导出的所有标识符，并且可以在不使用前缀的情况下进行访问。

period import 的规格如上所述很简单。
但是，由于以下理由不推荐使用。

*   无法声明与 period import 包中包含的函数同名的函数
*   变得难以分辨是哪个包的函数

作为例外，我认为在[“要在 Go 语言软件包的测试中调用私人函数和变量”](http://atotto.hatenadiary.jp/entry/2013/01/19/112012)中解说的用法非常有用。

### 期间导入的样品

```
package main

import . "fmt"

func main() {
    Println("period import")
} 
```

```
$ go run main.go
period import 
```

## 空白(_)导入

引自 Go 编程语言规范导入声明

> 导入声明声明了导入“属于”和“属于”之间的依赖关系。 导入自己的软件包或不引用导入的软件包中导出的任何标识符是一种错误的用法。 如果导入软件包只是为了导入副作用(初始化)，请使用空白标识符作为软件包名称。

这里可以在只执行 init 函数的情况下使用。
[effective go/import for side effect](http://golang.org/doc/effective_go.html#blank_import)中列举了为了调试 HTTP 而利用`net/http/pprof`软件包的方法。

### blank import 的样品

```
package main

import (
    "fmt"

    _ "./blank"
)

func main() {
    fmt.Println("main")
} 
```

```
package blank

import "fmt"

func init() {
    fmt.Println("blank/init")
} 
```

```
$ go run main.go
blank/init
main 
```

**Remember**

虽然最终会删除，但是在开发过程中有不想删除的 import 的情况下也可以使用 blank import。
如下所示，即使注释所有的`fmt`，import 也不会出错。

```
package main

import _ "fmt"

func main() {
    // fmt.Println("begin")
    anyFunction()
    // fmt.Println("end")
} 
```