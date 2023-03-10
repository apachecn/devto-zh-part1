# Golang -入门

> 原文：<https://dev.to/codehakase/golang---getting-started-16c>

> " Go 将成为未来的服务器语言."Tobias Lütke，Shopify

Go 最初是作为一项实验而被创造出来的，其创造者的目标是想出一种语言来解决其他人的坏习惯，同时保留好的东西。

它的首次发布是在 2012 年 3 月。Go 被设计得让人感觉熟悉并且尽可能简单，整个语言规范只有几页。

## 这个系列讲的是什么？

本系列是对 Go 编程语言的简单介绍，涵盖了 Go 的基本概念、语法和特性。本系列的最后部分将重点关注使用 Go 编程语言构建从零到活的应用程序。

## 为什么走？

为什么你会选择 Golang 而不是其他语言，比如 python、ruby、nodejs 等等？

以下是我见过的一些围棋高手:

*   并发是语言固有的一部分。因此，编写多线程程序是小菜一碟。这是通过 Goroutines 和 channels 实现的(将在本系列的下一部分中详细讨论)。

*   Golang 是一种编译语言。源代码被编译成本机二进制。这在解释型语言中是没有的，比如 nodejs 中使用的 JavaScript。

*   语言规范非常简单。整个规范适合一个页面，您甚至可以用它来创建自己的编译器...很甜蜜不是吗？

## 安装& &设置

Golang 在 Mac、Windows 和 Linux 这三个平台上都受支持。你可以在这里下载相应平台的二进制代码-[https://golang.org/dl](https://golang.org/dl)。

### Linux 设置

[下载](https://golang.org/dl)档案并解压到`/usr/local`，在`/usr/local/go`创建 Go 树。

```
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

将`/usr/local/go/bin`添加到**路径**环境变量中。您可以通过将这一行添加到您的/etc/profile(对于系统范围的安装)或`$HOME/.profile` :
来实现这一点

```
export PATH=$PATH:/usr/local/go/bin 
```

Enter fullscreen mode Exit fullscreen mode

### Mac OS X 包安装程序

[下载](https://golang.org/dl)包文件，打开，按照提示安装 Go 工具。这个包将 Go 发行版安装到`/usr/local/go`。

这个包应该将/usr/local/go/bin 目录放在 PATH 环境变量中。您可能需要重新启动任何打开的终端会话，以使更改生效。

### Windows 安装

打开 [MSI 文件](https://golang.org/dl/)，按照提示安装 Go 工具。默认情况下，安装程序将 Go 发行版放在 c:\Go 中。

安装程序应该把`c:\Go\bin directory`放到你的**路径**环境变量中。您可能需要重新启动任何打开的命令提示符，以使更改生效。

## 测试我们的安装

为了测试 Go 是否正确安装在我们的机器上，我们需要创建一个工作空间，并编写我们的第一个程序。

我们将首先创建我们的工作空间:

```
cd $HOME/go && mkdir test 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们用以下内容创建我们的源文件`test.go`:

```
 package main 

import "fmt"

func main() {

  fmt.Println("Hello World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码，代表了一个简单的围棋程序。每个围棋程序都是由包组成的。我们的程序，开始在软件包主程序中运行。该程序正在使用导入路径为“fmt”的包。

按照惯例，包名与导入路径的最后一个元素相同。例如，`fmt`包包含以语句包`fmt`开始的文件。

为了构建程序，Go 提供了一个构建工具供我们使用:

```
cd $HOME/go/test

go build test.go 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在源代码旁边的目录中构建一个名为 test 的可执行文件。执行它以查看问候语:

```
./test
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

如果您看到该问候语，则说明您目前为止已经成功，您的 Go 安装工作正常。

这是 Golang 教程系列的第一篇文章。在接下来的文章中，我将讨论以下主题:

*   变量- [视图](https://dev.to/codehakase/getting-started-with-go---variables-aef)
*   类型- [视图](https://dev.to/codehakase/types-in-go-ckf)
*   常量- [视图](https://dev.to/codehakase/constants-in-go-92o)
*   功能- [视图](https://dev.to/codehakase/functions-in-go-3dd)
*   包装
*   条件语句和循环
*   数组、切片和变量函数
*   指针、结构和方法
*   接口
*   并发
*   面向对象编程
*   延期和错误处理

我希望你喜欢这个系列的这一部分？你可以在我的[博客](https://hakaselabs.github.io)或[实用发展](https://dev.to/codehakase)上阅读我的其他文章。

如果我错过了什么，请在评论中告诉我。