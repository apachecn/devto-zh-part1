# 指针

> 原文：<https://dev.to/lumochift/pointer-201l>

在第一学期学习 C++的时候，指针给了我一些怀旧的时刻。它们成了我一天中头痛的来源。指针是在非垃圾收集语言中实现高性能代码的主要工具之一。那么围棋中的指针呢？幸运的是，Go 的指针通过提供具有垃圾收集器功能和易用性的高性能指针，实现了两者的最佳结合。

# 现实世界中的指针

不知不觉中我们已经知道了现实世界中指针的主要概念。现在网上市场很受欢迎，我们喜欢用智能手机或电脑购物。在结帐后，如果你想在你的房子里收到包裹，简单地发送你的房子的地址(指针)比将整个房子发送给卖家以便你的包裹存放在里面要容易得多。问题是如果你寄错了你的地址。你不会拿到包裹的。

让我们回到编程世界。例如，我有一个简单的程序将 mp3 文件转换成另一种格式。第一步，我必须读取 mp3 文件并保存到变量中，假设变量大小为 1GB，我需要将它传递给另一个函数。如果没有指针，整个变量将被克隆到将要使用它的函数的范围内。所以我将有 2GB 的内存被使用这个变量两次。如果第二个函数将再次传递给另一个函数，所占用的内存将会增加。

如果我用一个指针传递一个很小的引用给另一个函数，那么只有这个很小的引用被克隆，这样我就可以保持很低的内存使用率。

与 C 或 C++指针不同，在 GO 中非常有限。我们不能使用指针算法，也不能创建指针来引用堆栈中的确切位置。

下面是基本的[的例子](%5Bhttps://play.golang.org/p/FqVdZ6ntLN%5D(https://play.golang.org/p/FqVdZ6ntLN))的指针:

```
package main

import (
    "fmt"
    "unsafe"
)

type Music struct {
    SongName string
    TrackNo int
    Singer string
}

func main() {
    // declare a variable
    music := Music{"November Rain", 20, "Gun n Roses"}
    // set pointer of music to variable p
    p := &music
    fmt.Printf("music data %v\n", music)
    fmt.Printf("Pointer of music %p\n", p)
    // access original value using pointer
    fmt.Printf("Get music from pointer %v\n", *p)

    fmt.Printf("Original size: %T, %d\n", music, unsafe.Sizeof(music))
    fmt.Printf("Pointer size: %T, %d\n", p, unsafe.Sizeof(p))
}

// output:
// music data {November Rain 20 Gun n Roses}
// Pointer of music 0x10444240
// Get music from pointer {November Rain 20 Gun n Roses}
// Original size: main.Music, 20
// Pointer size: *main.Music, 4 
```

`music := Music{"November Rain", 20, "Gun n Roses"}`代码表示我们的 1GB 变量，`p`包含带有值`0x10444240`的引用(由一个&符号表示)。我们可以用星号`*`从指针引用的值中取值。在这个例子中，音乐数据的原始大小是 20 字节，而指针只有 4 字节。指针大小与 Go 中的 int 变量大小相同。即使我增加了结构/变量的大小，指针仍然保持不变。

希望你能理解。下一篇文章再见…