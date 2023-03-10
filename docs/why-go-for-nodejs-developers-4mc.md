# 为什么选择 Node.js 开发人员

> 原文：<https://dev.to/jorinvo/why-go-for-nodejs-developers-4mc>

下面的内容并不是试图让任何人相信一种技术比另一种好，相反，我喜欢探索每种技术的优点，以便我们可以更好地为给定的任务选择合适的工具。

我已经和 Node.js 一起工作了五年多了，在过去的一年里，我一直使用 [Go](https://golang.org/) 来构建各种各样的东西——更大的项目，还有[各种各样的](https://github.com/qvl/)开源工具。

在这一点上，我喜欢记录我在解决给定任务时选择这些语言的思维过程。

对于像我一样过去使用过 Node.js，现在听到很多关于每个人都成功转向 Go 的谈论的人来说，这篇文章可能是最有用的。

为了明确这一点，我们在这里讨论的是服务器端技术。有些人确实在浏览器中使用 [Go，但这不是本文要讨论的。](https://github.com/gopherjs/gopherjs)

还要注意的是，即使这篇文章或其他理由让你相信你正在尝试做的事情使用不同的技术会更好，一次性重写你的整个系统也不是一个好主意。找到可以轻松解耦的组件，并逐步进行更改。

另一件要记住的事情是不要把*“工作的正确工具”*发挥到极致。不要低估同时处理多个生态系统的复杂性。在你的系统中引入新技术时要小心。复杂性总是伴随着成本。

* * *

说了这么多，还是说说围棋吧。

在使用 Node.js 时，您可能会遇到一些问题，这些问题可以通过使用 Go 来解决。还有其他问题 Go 不会为你解决。没有什么灵丹妙药。

* * *

*如果你遇到以下问题之一，你可能想看看 Go:*

*   您的软件需要在可用内存很少的硬件上运行，或者您的节点应用程序在其他方面使用了超出可接受范围的内存。

我们来比较一下这两个小程序的内存使用情况，第一个在 JavaScript，第二个在 Go:

```
setTimeout(() => {}, 100000) 
```

Enter fullscreen mode Exit fullscreen mode

```
package main
import "time"
func main() { time.Sleep(100 * time.Second) } 
```

Enter fullscreen mode Exit fullscreen mode

在我的笔记本电脑上，JavaScript 进程使用 8.6MB，而 Go 进程使用 380KB。这种差异并不令人惊讶，因为 Go 是预先编译成机器代码的，并且运行时间非常短，但是对于某些类型的软件来说，这是需要注意的。

*   应用程序需要**尽快启动**,因为它会频繁重启，或者您正在使用 CLI 工具之类的东西。

虽然 Node.js 与许多其他运行时相比有很好的启动时间，但它赶不上 Go:

```
console.log('hello') 
```

Enter fullscreen mode Exit fullscreen mode

```
package main
import "fmt"
func main() { fmt.Println("hello") } 
```

Enter fullscreen mode Exit fullscreen mode

当使用`time`命令运行这两个程序时，运行节点版本需要大约 120ms，而运行编译后的 Go 程序需要 10ms。

*   服务正在做的工作是**计算密集型的**和 **CPU 受限的**。

与 Python 或 Ruby 等其他环境相比，Node.js 经常因其在 web 应用程序方面的性能而受到称赞。这种性能来自 JavaScript 运行时的异步编程模型。通过利用事件循环和异步函数，单个进程可以同时执行多个任务。然而，这只适用于受 IO 限制的任务——也就是说，由于必须等待网络或磁盘而导致速度缓慢的任务。这类任务在 web 应用程序中非常常见，因为它们经常需要从其他资源(如磁盘上的文件、数据库或第三方服务)获取信息。

如果您的性能受到原始计算能力的限制，Go 可能是一个有趣的选择。通过其静态类型系统和直接编译成机器码，它的性能可以得到更好的优化，在许多情况下，它比任何 JavaScript 引擎都快。

此外，Go 可以并行运行代码。虽然 Node.js 有很好的并发模型，但它不支持并行执行。Node.js 进程总是在单个线程中运行。Go 可以利用机器提供的所有 CPU，并且 Go 带有内置于语言中的简单并发原语。通过使用 Goroutines 和 channels，人们有了一种简单的方法来编排一个并行系统，而不依赖于互斥体和手动资源锁定。

如果您的问题是 CPU 受限的，甚至可能是并行的，那么 Go 应该能够比 Node.js 提供更好的性能。

在极端情况下，Go 的性能会提高 N 倍——N 是程序可以利用的内核数量。但是请记住，在许多情况下，您可以通过简单地运行更多进程来扩展节点。与线程级别相比，进程级别的伸缩会带来一定的开销，但是除非您也受到上述限制之一的约束，否则这对您来说可能不是问题。协调多个进程的最简单方法是使用节点的[集群模块](https://nodejs.org/api/cluster.html)。我也鼓励你看看其他技术，比如 [ZeroMQ](http://zeromq.org/) 。

*   您的应用程序的**部署**受限于机器上没有额外的**依赖**可用，或者受限于部署允许使用的**文件大小**。

Node.js 需要安装在主机上。此外，需要复制所有文件，并使用`npm install`在机器上安装依赖项。依赖项通常包含本机 C 库，必须提前安装在主机上。

在 Go 中，整个程序和所有依赖项都可以编译成一个单一的、静态链接的二进制文件。二进制文件可以在任何平台上交叉编译。

上述 *hello* Go 程序的 Linux 二进制文件大小为 **1.2MB** 。

如果系统使用 Docker 容器，节省的文件大小会更大:

使用以下 docker 文件构建节点版本会产生 676MB 的映像。

```
FROM node
WORKDIR /usr/src/app
COPY index.js .
CMD ["node", "index.js"] 
```

Enter fullscreen mode Exit fullscreen mode

使用以下 docker 文件生成的 Go 二进制文件的图像大小为 1.23MB

```
FROM scratch
COPY hello /
ENTRYPOINT ["/hello"] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果有许多容器在运行，并且对它们使用相同的基本映像，那么它将被重用，并且磁盘空间只使用一次。

还有用于运行 Node 的[轻量级](https://hub.docker.com/_/node/)替代容器 230MB 的`node:slim`和 67.5MB 的`node:alpine`。

如果没有任何外部依赖，Go 容器只能这么小。否则，您可能还需要一个 Alpine 或 Debian 的 Go 图像，并以类似的图像大小结束。还要记住，要创建一个小的 Go 容器，你需要一个更复杂的构建过程，因为你需要首先创建二进制文件，然后将它复制到一个容器中。

* * *

还有许多其他软因素是人们决定改用 Go 的基础:

*   与 JavaScript 中的 3+种方法相比，Go 只有一种错误处理模式。
*   Go 在默认工具链中内置了方便的工具，用于测试、记录和格式化代码。
*   静态类型允许紧密的编辑器集成，包括自动完成、内联文档、转到定义、重命名符号等

在我看来，这些争论都不能证明重写一个现有的代码库是正确的，投资于改进你的 JavaScript 编码指南可能更有益，使用像[beautiful](https://github.com/prettier/prettier)这样的工具，并编写适当的文档和测试，这在 JavaScript 中同样是可能的。

* * *

如果上面的任何一个论点说服了你，那么 Go 可能是一个更适合你正在试图解决的问题的工具，请记住，还有其他语言与 Go 有许多共同的特征。如果你的问题是非常关键的性能，一个可能更适合的解决方案可能是一种语言，如 Rust 或 c。Go 仍然带有一个运行时，并使用垃圾收集，可以在任何时候暂停你的程序。你会考虑 Go 而不是 Rust 的主要原因是因为入门的门槛要低得多。围棋是一种更简单的语言，你需要记住的概念更少。人们可以非常快速地开始工作并富有成效。

* * *

### 不用时走

如果以上几点都与你想达到的目的无关，你也可以使用除了 Go 之外的任何语言。没有什么好的理由让你扔掉所有的工作，用另一种语言重写。

事实上，我认为坚持 Node 可能会更有成效。JavaScript 及其生态系统附带了许多强大的工具和抽象，这允许您更多地考虑您的问题领域，而不是技术实现的细节。

能够在 REPL 中加载您的代码，并现场试用和检查您的数据，可以让您非常快速地探索想法。如果您编写自动化测试——在任何情况下您都应该这样做——您还会发现静态类型可以为您发现的问题。

在这两个程序中，你更喜欢哪一个来写、读和推理？

这:

```
const toInts = strings => strings.map(s => parseInt(s, 10))
console.log(toInts(['1', '2'])) 
```

Enter fullscreen mode Exit fullscreen mode

还是这个:

```
package main

import (
    "fmt"
    "strconv"
)

func toInts(strings []string) ([]int64, error) {
  var res []int64

  for i, s := range strings {
    r, err := strconv.ParseInt(s, 10, 64)
    if err != nil {
      return res, fmt.Errorf("failed parsing element at index '%d': %v", i, err)
    }
    res = append(res, r)
  }

  return res, nil
}

func main() {
  fmt.Println(toInts([]string{"1", "2"}))
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，如果你想深入讨论静态语言和动态语言，我向你推荐这篇有趣的文章。

* * *

如你所见，没有正确的答案。这取决于你的问题。即便如此，也可能没有明显的赢家。

也就是说，探索一种新的语言及其思维方式从来都不是一个坏主意。如果你喜欢看 Go，我推荐你在这里看看这个对比:
[JavaScript 开发者的 Go](https://github.com/pazams/go-for-javascript-developers)