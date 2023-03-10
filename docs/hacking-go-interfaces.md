# 入侵 Go 接口

> 原文：<https://dev.to/loderunner/hacking-go-interfaces>

# 简介

通常，在编程中，接口先于实现。我们知道我们希望我们的架构的各个部分能够做什么，它们将如何表达，并且我们只有在确定这一点后才开始编写代码。在我们分解它们的内部工作之前，接口使得“从外到内”的推理成为可能，决定更大部分的行为。

但通常，我们是“由内而外”地“入侵”我们的方式，首先构建一些我们知道是必不可少的部分，然后将它们组合在一起或包装成更大的部分。我认为有一种方法可以从内到外展示为什么以及如何在 Go 中使用接口。

# 我们应该黑什么？

我决定黑一个(非常幼稚的)日志框架。这个想法是:每个人都知道日志框架应该做什么，这有点像拥有一个接口(概念上)。此外，我不需要介绍任何可能分散我们注意力的具体概念。最后，一个日志框架是典型的那种我们开始破解的东西，包装成一些其他的部分，然后当我们需要改进它的时候再回来。

因此，在没有任何预先设计的情况下，让我们开始在 Go 中构建一个日志框架。我将每一章都提交到了一个 GitLab 库中，这样你就可以在代码库中跟踪章节的进展。每一章都链接到阅读材料中的那一点。

# [版本 1–标准输出](https://gitlab.com/loderunner/ezlog/tree/version-1)

我的第一个包版本只有一个包级别的函数，`Log`，它将把消息输出到标准输出，在消息被记录的时间之前。(Go 有一个非常有趣的方法将时间[格式化为字符串](https://golang.org/pkg/time/#pkg-constants)，比旧的 [strftime](http://pubs.opengroup.org/onlinepubs/009695399/functions/strftime.html) 要容易得多。)

包装:

```
// Package ezlog is a naive log implementation
// used as experiment and tutorial in learning Go
package ezlog

import (
    "fmt"
    "time"
)

// Log logs a message to standard output preceded
// by the time the message was emitted
func Log(msg string) {
    now := time.Now()
    fmt.Printf("[%s] %s\n", now.Format("2006-01-02 03:04:05"), msg)
} 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
package main

import "gitlab.com/loderunner/ezlog"

func main() {
    ezlog.Log("Hello World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ go run examples/hello.go
[2017-07-25 06:20:55] Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

这没什么特别的，但是很有效。我们现在可以使用这个包开始记录标准输出。当然，这还远远不够。

# [版本 2–文件记录](https://gitlab.com/loderunner/ezlog/tree/version-2)

接下来我们要记录的是一个文件。我们需要软件包的客户端在开始调用`Log`之前给出一个文件的路径。这告诉我们，我们的包现在需要一个*初始化*的步骤，然后才能使用。让我们添加一些代码。

我们在包中添加了一个`Open`函数，如果成功，它会打开一个文件并将其设置为一个包变量。

```
var logfile *os.File

// Open opens a file to append to, creating
// it if it doesn't exist
func Open(path string) error {
    f, err := os.OpenFile(
        path,
        os.O_WRONLY|os.O_APPEND|os.O_CREATE,
        0644,
    )
    if f != nil {
        logfile = f
    }
    return err
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在`Log`函数中记录文件。

```
func Log(msg string) {
    // Format the output string
    now := time.Now()
    output := fmt.Sprintf("[%s] %s", now.Format("2006-01-02 03:04:05"), msg)

    fmt.Println(output)           // Log to stdout
    fmt.Fprintln(logfile, output) // Log to a file
} 
```

Enter fullscreen mode Exit fullscreen mode

我们扩展了测试程序，添加了文件初始化。

```
func main() {
    ezlog.Open("hello.log")
    ezlog.Log("Hello World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行这个程序时，我们在目录中发现一个新文件`hello.log`。它包含与我们在控制台中看到的相同的输出。

```
$ go run examples/hello.go
[2017-07-25 09:22:21] Hello World!
$ cat hello.log
[2017-07-25 09:22:21] Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

"我们可以记录到标准输出和一个文件，我们仍然不需要接口！"嗯，是的。但是我们开始看到我们建筑的局限性。如果我们需要记录两个文件呢？如果我们不想记录标准输出呢？如果我们想登录到`syslog`或者通过网络登录会发生什么？另外，代码开始看起来有点难看。

# 版本 3–记录器

## [伐木工](https://gitlab.com/loderunner/ezlog/tree/version-3-loggers)

以上问题的答案不是:添加包级变量，初始化函数，把`Log`做成单片函数。我们希望这是模块化的。我们需要的是任意数量的“记录器”,我们可以对其进行配置，并交给软件包来完成工作。让我们写两个`struct`来封装这两个行为。一个用于标准输出，一个用于文件。

标准输出记录器:

```
// StdoutLogger logs messages to stdout
type StdoutLogger struct{}

// NewStdoutLogger StdoutLogger constructor
func NewStdoutLogger() *StdoutLogger {
    return &StdoutLogger{}
}

// Log logs the msg to stdout
func (l *StdoutLogger) Log(msg string) {
    fmt.Println(msg)
} 
```

Enter fullscreen mode Exit fullscreen mode

文件记录器:

```
// FileLogger logs messages to a file
type FileLogger struct {
    f *os.File
}

// NewFileLogger opens a file to append to and
// returns a FileLogger ready to write to the file
func NewFileLogger(path string) (*FileLogger, error) {
    f, err := os.OpenFile(
        path,
        os.O_WRONLY|os.O_APPEND|os.O_CREATE,
        0644,
    )
    if err != nil {
        return nil, err
    }
    return &FileLogger{f}, nil
}

// Log logs a message to the file
func (l *FileLogger) Log(msg string) {
    fmt.Fprintln(l.f, msg)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经隔离了记录器，如何将它们放在一起呢？

```
var Stdout *StdoutLogger
var File *FileLogger

// ... omitted code here ...

    if Stdout != nil {
        Stdout.Log(output) // Log to stdout
    }
    if File != nil {
        File.Log(output) // Log to a file
    } 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很傻。如果我们想要更多的文件呢？一组`FileLogger` s？对于其他类型的日志记录器，仍然没有合适的解决方案。

## [界面](https://gitlab.com/loderunner/ezlog/tree/version-3-interface)

很容易看到主包的`Log`函数只是准备输出，然后在每个记录器上调用`Log`。但是 Go 中的静态类型阻止了我们仅仅在运行时解析调用。编译器需要确保无论我们给它什么，它都知道如何`Log`。

“不管这是什么，我用得着`Log`”，难道*正是*在围棋中所做的界面。当你要求一个接口类型作为一个函数的参数时，你的代码告诉编译器的是:“这个函数接受一个指向某个东西的指针，我不在乎它是什么，只要它有来自接口的所有方法”。当从一个函数返回一个接口类型时，你在说:“不要问这是什么，你需要知道的是它做*这个*”。您可以将接口想象成底层类型需要满足的契约或需求。

这里，我们的两个记录器都实现了一个`Log`方法，所以我们可以说它们有一个共同的`interface`。让我们把它砍了。

```
type Logger interface {
    Log(string)
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。通过写这几行，我们只是定义了一个`Logger`接口。不需要对`StdoutLogger`和`FileLogger`做任何改动。编译器将检查他们是否有`Log`方法来确定他们是否支持该接口，而无需我们给出任何指示。任何来自 C++或 Java 的人都会知道这有多酷。如果我们扩展接口来添加新方法，如果我们尝试使用不符合的类型，它仍然会在编译时中断，因为它不会实现新方法。

我们现在可以开始使用`Logger`作为类型。

```
var loggers []Logger

// AddLogger adds a logger to the list
func AddLogger(l Logger) {
    loggers = append(loggers, l)
}

// Log logs a message to standard output preceded
// by the time the message was emitted
func Log(msg string) {
    // Format the output string
    now := time.Now()
    output := fmt.Sprintf("[%s] %s", now.Format("2006-01-02 03:04:05"), msg)

    // Log to all loggers
    for _, l := range loggers {
        l.Log(output)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。它只是工作。通过创建一个接口来声明一个方法，我们的两个记录器都遵循这个方法，我们可以用几行代码很好地将这些结合在一起。

让我们举一个更复杂的例子，看看我们现在能做什么。

```
func main() {
    // Log to stdout
    ezlog.AddLogger(ezlog.NewStdoutLogger())

    for now := range time.Tick(1 * time.Second) {
        // The seconds of the current time ends with 5
        if (now.Second() % 5) == 0 {
            // Add a new file to log to
            filename := fmt.Sprintf("gopher-%s.log", now.Format("030405"))
            fileLogger, err := ezlog.NewFileLogger(filename)
            if err == nil {
                ezlog.AddLogger(fileLogger)
            } else {
                ezlog.Log("Couldn't open new file.")
            }
        }

        ezlog.Log("Gopher!")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此示例每隔 5 秒钟向一个新文件中动态添加记录器。它是这样工作的。

[![asciicast](img/84b5ff54c3716be430f71af88ff0258e.png)T2】](https://asciinema.org/a/TUkN1WXIoBVie4MJoKq7zQQWC)

# 接下来呢？

嗯，有很多事要做。

这个框架绝不是并发安全的。如果几个 goroutines 同时调用`AddLogger`,我们将结束对`loggers`数组的数据竞争，并可能丢失数据。见鬼，它本身甚至不是并发的！我们可以通过为不同类型的记录器使用 goroutines 和通道来优化它。我们甚至可以使用 Go 内置的缓冲通道来实现廉价的日志假脱机。

我们可能想要实现更多的日志记录器。事实上，我已经黑掉了一个[网络](https://gitlab.com/loderunner/ezlog/blob/f3bd1f2745e9d788a7af91bf1574aee69e1599d8/net.go)和一个 [`syslog`](https://gitlab.com/loderunner/ezlog/blob/f3bd1f2745e9d788a7af91bf1574aee69e1599d8/syslog.go) 日志程序(这实际上是一个有趣的经历，我会在另一篇文章中谈到)。

以下是我仍想添加的一些功能列表:

*   分级日志
*   日志格式
*   命名记录器
*   并发和优化
*   ...移除记录器(比看起来更棘手)

最后，这个软件包也是一个有趣的学习围棋的平台。我打算继续摆弄一段时间，看看我能做些什么。查看存储库，了解它的进展情况。请随意评论、提出想法，甚至为该包做出贡献。