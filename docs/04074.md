# 在 Node.js 上，Go 和并发

> 原文：<https://dev.to/edneypitta/on-nodejs-go-and-concurrency-c8n>

*最初发表于[我的博客](https://edneypitta.com/on-node-go-concurrency/)。*

在最近的一次采访中，Node.js 的创始人瑞安·达尔(Ryan Dahl)在谈到 Node 的并发模型时说了下面这段话:

> [...]我认为 Node 并不是构建海量服务器 web 的最佳系统。我会用 Go 来表示。老实说，这就是我离开 Node 的原因。人们意识到:哦，实际上，这并不是最好的服务器端系统。

太强了。为什么当时努力构建和倡导 Node.js 的 Dahl 会放弃他的创作而推广其他东西呢？这对 Node.js 及其庞大的社区意味着什么？

让我们先快速回顾一下。

## Node.js 历史

Node.js 的灵感来自 NGINX 事件驱动架构非阻塞 I/O。基本上，这意味着我们不再依赖线程实现并发性(无论是 OS、green 还是协程)，而是只有一个线程通过事件循环来协调异步工作。

Dahl 随后构建了 Node.js 来实现这一点，混合了其他工具，如简化的 web 服务器 API、V8 和 JavaScript，这是单线程的。

他非常相信这个并发模型。在他关于 Node.js 的第一次演讲中，他声称我们一直在做错误的 I/O。处理多线程会误导开发人员，并且由于上下文切换和每个线程占用的执行堆栈，会导致 CPU 和内存的更高消耗。

鉴于 Node.js 无可否认的成功，我们只能假设他是对的。然而，是什么让他如此戏剧性地改变了主意呢？

## Go 的并发模型

Go 是一种为并发性而构建的语言。它基于 CSP(通信顺序进程)，一种由东尼·霍尔在 1977 年描述的模式。

长话短说，Go 是多线程，阻塞 I/O，为什么不慢？诀窍在于这都是由 Go 的运行时管理的:当你创建一个 *goroutine* 时，你实际上并没有创建一个线程(既不是一个协程)。Go 所做的是将独立的协同程序组合到一组线程上，因此当一个协同程序阻塞时，运行时会自动将其他协同程序移动到一个不同的可运行线程上。

另一个重要的部分是，这些 goroutines 通过*通道*进行通信，因此它们传递对数据的引用，而不是在线程间共享内存(这会引入可怕的锁定问题)。这确保了在任何给定时间只有一个 goroutine 可以访问数据。

> 不通过共享内存进行交流；相反，通过交流来分享记忆。

显然，这很有效。Go 的发展令人惊叹，如今它是并发解决方案的可靠选择。当然有一些批评——通常是与 Erlang 的演员模式相比——但它的成功是显而易见的。有很多关于人们从其他语言迁移到其他语言的成功故事，它们确实有道理。

## 结论

那么 Node.js 会发生什么呢，因为有一种语言可以证明它有更好的并发模型？

嗯，**没事**。就并发性而言，Go 可能比 Node 更好，但是如果您没有构建大型 web 服务器，这并没有太大的区别。即使你是，你也可以向外扩展(在 Node 的标准库中有一个内置模块)。还是关于并发性，围绕 Node 模型的最有争议的问题之一已经解决:回调地狱。从 7.6 版本开始，Node 支持异步/等待开箱即用，不需要回调，仍然不阻塞 I/O。

但我发现整个故事中最有趣的是，Node 之所以流行，可能不仅仅是因为它革命性的并发模型。事实上，当时还有其他的库在其他语言中做非阻塞 I/O，比如 Python 的 Twisted 和 Java 的 Netty。

使 Node 成为构建 web 应用程序的最大平台之一的正是它的社区和生态系统。原因是:

*   首先，语言:JavaScript 已经在前端 web 开发中大量使用。能够在后端使用它意味着你可以成为只使用一种语言的全栈开发人员，这降低了 web 开发的障碍。
*   然后，并发模型。但不仅仅是因为它的性能；关键是**每个人**都开始基于这个新模型构建库，不像我上面提到的库，它们是线程化的非阻塞 I/O。
*   因为生态系统是一个重要的因素，有一个设计良好的包经理，NPM，当然也有帮助。

而这些东西永远不会消亡~~直到下一次炒作~~

感谢阅读！

## 参考文献及进一步阅读

*   [瑞安·达尔采访](https://www.mappingthejourney.com/single-post/2017/08/31/episode-8-interview-with-ryan-dahl-creator-of-nodejs/)
*   [HN 讨论](https://news.ycombinator.com/item?id=15140669)
*   [节点的第一次展示](https://www.youtube.com/watch?v=ztspvPYybIY)
*   [Rob Pike(Go)优谈并发不是并行](https://www.youtube.com/watch?v=cN_DpYBzKso)
*   [Rob Pike 的 talk Go 并发模式](https://www.youtube.com/watch?v=f6kdp27TYZs)
*   [Go 关于并发的文档](https://golang.org/doc/effective_go.html#concurrency)
*   [对 Go 并发模型的伟大批判](https://gist.github.com/kachayev/21e7fe149bc5ae0bd878)