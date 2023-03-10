# 小心被发现。Return()及其调度程序

> 原文：<https://dev.to/jonstodle/beware-observablereturn-and-its-scheduler>

*这篇文章最初发表在我的博客上[jonstodle.com](https://jonstodle.com/2017/03/30/beware-of-observable-return-and-its-scheduler/)T3】*

我喜欢[反应式扩展](http://reactivex.io/)。它让异步变得更加容易，也让处理连续的数据流或事件变得更加容易。

虽然反应式扩展(Rx)在默认情况下是单线程的，但在需要时可以很容易地在线程之间移动。如果您需要做一些繁重的工作，您可以很容易地告诉 Rx 将这些工作移动到任务池上的一个线程，然后及时移回到 UI 线程来更新 UI。

Rx 中的一些操作员接受所谓的调度程序，它告诉 Rx 在哪里做操作员的工作。这些操作符经常做一些可能阻塞线程的工作，因此可以很容易地将它们的工作转移到另一个线程。

`Observable.Return()`也接受一个调度器，告诉它在哪个线程上返回对象。虽然它做到了这一点，但也有一些事情它没有做到:它不会将返回的对象移动到请求的线程，直到它检索到它。

这意味着，如果您通过阻塞函数或方法调用获取值以返回，它将阻塞您当前所在的线程。

这里有一个例子。首先我们打印当前线程的 id:

```
Console.WriteLine("Starting on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString()); 
```

然后我们创建一个简单的`Func`，它打印运行它的线程，然后返回一个`Unit`，它只是一个空值。我们将在`Observable.Return`中运行这个来检查它在哪个线程上。

```
Func writeLineAndReturnUnit = () =>
{
    Console.WriteLine($"Inside Return on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString());
    return Unit.Default;
}; 
```

我们调用`Observable.Return`并指定它应该在任务池线程上返回。在`Observable.Return`给了我们一个值之后，我们立即检查我们所在的线程，然后当我们收到值时，在 subscribe 中再次检查。

```
Observable.Return(writeLineAndReturnUnit(), TaskPoolScheduler.Default)
    .Do(_ => Console.WriteLine("Passing through on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString()))
    .Subscribe(_ => Console.WriteLine($"Received on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId)); 
```

这将输出以下内容(当然，线程 id 会有所不同)。

```
       Starting on thread 11
  Inside Return on thread 11
Passing through on thread 10
       Received on thread 10

```

如您所见，`Return`中的代码是在调用它的同一个线程上执行的。如果你调用一个函数或方法阻塞了`Return`内部的线程，比如一个文件可能需要一些时间从磁盘加载，你将阻塞当前的线程。如果您从 UI 线程调用它，这可能不是您想要的。

教训是:不要在看得见的地方做任何重物。返回，它只有在获取了要返回的值后，才会移动到正确的线程。最好只返回“简单”的值，如字符串或整数。