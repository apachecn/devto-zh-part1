# Ruby 中的基本并发。真的很基础。

> 原文：<https://dev.to/briankephart/basic-concurrency-in-ruby-really-basic-35oi>

作为一名新程序员，我经常发现文档没什么帮助。或者至少很难解析。最近，我在阅读 concurrent-ruby gem 的文档时遇到了这个问题。我想说清楚，这个宝石的文档并不坏。相反，它是密集和彻底的，这对于查找一个简单的用例是有害的。我很难把它分解成我所需要的，所以我在这里展示我的结果，作为其他人的参考。

CRuby 并不擅长单个进程内的并发性(比如单个 web 请求)。您可以在一个进程中创建新线程，但是由于全局解释器锁，这些线程将一次执行一个。这意味着你不能同时执行两条指令，但是你可以启动多个操作，Ruby 会在资源允许的情况下在它们之间切换。在实践中，这通常在调用外部服务时非常有用。您可以启动一个 i/o 操作，然后在等待结果的同时执行一些代码。

前一段是我从网上各种阅读中知道的。不过，我不知道如何用代码实现它。很长一段时间我都不需要。

我们的业务使用第三方日程安排软件，我需要为我正在构建的应用程序对该软件进行一些 API 调用。这些 API 调用很慢(每次几秒钟)，所以我想同时调用它们，而不是顺序调用。这是 CRuby 中并发性的典型案例，因为大部分时间花在等待 i/o 上，而不是代码执行上。

下面是我想同时做的事情:

```
var_1 = some_api_call_result
var_2 = some_other_api_call_result
# some code that uses var_1 and var_2 
```

Enter fullscreen mode Exit fullscreen mode

而下面是我最后是怎么做到的:
*【注:不使用 concurrent-ruby gem，使用 ruby 标准库，如何做到这一点，请看评论！】*T3】

```
var_1 = Concurrent::Future.execute { some_api_call }
var_2 = Concurrent::Future.execute { some_other_api_call }
var_1 = var_1.value
var_2 = var_2.value
# some code that uses var_1 and var_2 
```

Enter fullscreen mode Exit fullscreen mode

`execute`方法告诉解释器运行 API 调用并继续程序执行，同时等待它完成。`value`方法返回传递给`execute`方法的代码块的结果，如果必要的话，在操作完成时阻止程序执行，确保这些值存在，以便执行后面的代码。

使用这种模式，通过创建`Concurrent::Future` ( [docs](http://ruby-concurrency.github.io/concurrent-ruby/Concurrent/Future.html) )对象并将所需代码作为一个块传递给`execute`方法，可以异步运行 i/o 操作。在程序需要 i/o 操作的结果之前，不需要阻塞程序执行，此时使用`value`方法来获得它。

对我来说，这似乎是 Ruby 程序中最基本的并发形式，但我很难找到一种真正简单的方式来表达它。我希望它能帮助第一次处理并发性的其他人，并且我鼓励其他人张贴他们关于难以学习的基本操作的故事。