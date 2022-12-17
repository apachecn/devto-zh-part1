# Golang 指针概述

> 原文：<https://dev.to/trknhr/summary-of-pointer-of-golang-6bo>

有时我会弄不清如何从指针变量中检索。所以我想总结一下 Golang 的要点，不要忘记他们。因为我没有使用过有指针变量的语言。

那对我来说只是备忘录。

*   代表 value 的内存地址。
*   *(在值前面)代表指针的基础值
*   *(在类型名前面)代表存储另一个变量的地址

这是一个例子

```
package main

import "fmt"

type H struct {
    id *int
}

func main() {
    a := 10

    p := &a
    fmt.Println(p)  //0x10410020
    fmt.Println(*p) //10

    a = 11
    fmt.Println(p)  //0x10410020
    fmt.Println(*p)

    q := 90
    b := H{id: &q}
    fmt.Println(b.id)   //0x1041002c
    fmt.Println(*b.id)  //90

    q = 80
    fmt.Println(b.id)   //0x1041002c
    fmt.Println(*b.id)  //80
} 
```

Enter fullscreen mode Exit fullscreen mode