# 任务。ToObservable()！=可观察。FromAsync(() => Task)

> 原文：<https://dev.to/jonstodle/tasktoobservable--observablefromasync--task>

当把任务转换成可观察的东西时，编写这样的东西是很常见的:

```
Observable.FromAsync(() => ThisReturnsATask()); 
```

在某些时候，你可能会发现，实际上有一种任务的扩展方法，可以将其转化为可观察的:

```
ThisReturnsATask().ToObservable(); 
```

但这与第一次转换不同。

当调用`ThisReturnsATask()`时，它会像往常一样立即启动任务。它返回的任务可以存储在一个变量中…

```
var task = ThisReturnsATask(); 
```

…稍后可以等待以检索值:

```
var result = await task; 
```

如果任务已经完成，它将立即返回结果。如果没有完成，执行将等待它完成。它的行为和你这样做时完全一样:

```
var result = await ThisReturnsATask(); 
```

当调用`ThisReturnsATask().ToObservable()`时，它将立即启动任务，并将结果转换成可观测的信号。如果你还没有订阅可观测值，它会一直保存这个值，直到你准备好接受它。

这与`Observable.FromAsync(() => ThisReturnsATask());`相反，它将调用内部的`Func<Task>`推迟到您订阅之后。这意味着从`ThisReturnsATask()`返回的任务将不会开始，直到你订阅了可观测的。

这里有一个例子:

```
async Task DoWork()  
{
    System.Diagnostics.Debug.WriteLine("I did work");
}

var obs1 = Observable.FromAsync(() => DoWork());  
var obs2 = DoWork().ToObservable(); 
```

如果您运行这段代码，您可能认为输出窗口中不应该打印任何内容，但是您将看到的是

```
I did work 
```

这是因为当你赋值`obs2`时，`DoWork`被调用并打印到调试窗口。`obs1`在您订阅之前不会打印任何内容。

顺便提一句，你可以用这个“变通办法”让`ToObservable()`表现得像`FromAsync()`:

```
Observable.Defer(() => ThisReturnsATask().ToObservable()); 
```

对我来说感觉有点重手，所以我个人更喜欢`FromAsync()`。

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/task-toobservable-observable-fromasync-task/)T3】*