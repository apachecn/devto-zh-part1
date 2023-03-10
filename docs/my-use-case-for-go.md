# 我的围棋用例

> 原文：<https://dev.to/mariosangiorgio/my-use-case-for-go>

在使用了几个用 Go 编写的非常好的应用程序之后(例如 [Syncthing](https://syncthing.net/) 、 [Docker](https://www.docker.com) 和 [Hugo](https://gohugo.io/) )，我想开始学习更多关于这门语言的知识。

我对编程语言理论非常感兴趣，它如何给开发人员提供他们需要的工具，以尽可能好的方式编写软件，并尽可能多地保证最终应用程序的正确性。

要了解编程语言理论的发展方向，可以看看《华盛顿邮报》(Rust 的创造者，现在是 Swift 的开发者之一)发表的讨论编程语言可能的新研究方向的文章。

很明显，我并没有期望 Go 能够解决这些问题，但是我发现非常有趣的是，它的设计者以简单的名义故意忽略了许多我认为是[基础功能](http://yager.io/programming/go.html)的东西。
缺乏泛型经常在关于 Go 的讨论中被提及。
我希望 Go 默认拥有代数数据类型和不变性。我很乐意放弃 [*nil*](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare) 来获得这些特性。

从积极的一面来看，Go 有好的库、好的工具、通用的风格和非常容易掌握的语法。它足够快，并且通过 [goroutines](https://tour.golang.org/concurrency/1) 很好地支持并发。它还可以生成可执行文件，这些文件非常容易部署到任何地方。

鉴于这种描述，在我看来，Go 是 C 和 Python 的一种进化，我决定尝试[重写我正在开发的最初用 Python 编写的项目](https://github.com/mariosangiorgio/dunbar-go)。

那个项目非常简单，规模小，自成一体。它与 Twitter 互动(一个好的图书馆是一个巨大的优势)，它分析我与我关注的人的互动。我的目标是发现我是否在关注那些我不常交流的人。
不知何故，我关注了太多人，我需要找到一种方法，让我的关注人数更容易控制。

不可否认，这太简单了，无法下结论，但我认为它仍然足以让我对这种语言有一种感觉，并且我有一个好的印象。学习这门语言很容易，我使用的库很好。关于 Python，我有一个类型系统，尽管有限，而且我得到了一个可执行文件，我可以轻松地部署它，而不需要额外的依赖。

有了这样的经验，我相信我会再次考虑为类似规模和范围的项目或简单的命令行应用程序使用 Go。对于这个用例，我认为它比 Python 更适合，同时它的简单性可以让我比使用其他语言更有效率。

最初发布于[mariosangiorgio . github . io](https://mariosangiorgio.github.io/post/my-use-case-for-go/)