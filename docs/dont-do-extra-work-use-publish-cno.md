# 不做额外的工作，使用 Publish()

> 原文：<https://dev.to/jonstodle/dont-do-extra-work-use-publish-cno>

当使用[反应式扩展](http://reactivex.io/)时，你迟早会遇到这样一种情况，你最终会重用同一个源可观测量来构建两个不同的可观测量。大概是这样:

```
var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))  
    .SelectMany(_ => LoadSomethingFromTheWeb());

somethingsFromWeb  
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });

somethingsFromWeb  
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ }); 
```

你可能没有意识到的是，你实际上是在做双重工作。每次你在一个可观察对象上调用`Subscribe()`，你实际上是在创建一个全新的可观察管道。尽管`somethingsFromWeb`在创建可观察管道时在两个订户之间共享，但当通过调用`Subscribe()`来订阅时，它并不共享。

一种说法是将`somethingsFromWeb`复制给使用它的每个订户。上面的代码和下面的代码有些等价，用的是 good ol’。网络事件:

```
window.Activated += async (sender, args) =>  
{
    var somethingFromWeb = await LoadSomethingFromTheWeb();
    if (somethingFromWeb.Length > 10) { /* Do something */ }
}

window.Activated += async (sender, args) =>  
{
    var somethingFromWeb = await LoadSomethingFromTheWeb();
    if (somethingFromWeb.Length <= 10) { /* Do something */ }
} 
```

这样说的话，很容易看出发生了什么:很明显，你在为每个事件多次做同样的工作。

当然，在反应式扩展中有一种正确的方法，那就是`Publish()`操作符。这使得第一个例子中的`somethingsFromWeb`的行为像您所期望的那样:它在所有订阅者之间共享可观察的源代码。

通过这样做…

```
var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))  
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish(); 
```

……`Publish()`之前的所有事情都完成一次，然后使用`somethingsFromWeb`作为源传递给所有订户。

当对一个可观察对象使用`Publish()`时，你也阻止了它产生任何元素。除非你告诉它，否则它不会发射任何东西。这可能看起来有点奇怪，但是在您希望确保使用它作为源的多个订阅者在它开始发射元素之前已经准备好的情况下，这真的很有用。

为了让它开始，你调用`Connect()`上的“已发布的可观察值”(技术上称之为“可连接的可观察值”):

```
var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))  
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish();

somethingsFromWeb  
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });

somethingsFromWeb  
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ });

somethingsFromWeb.Connect(); 
```

在这里，它是在两个订阅者建立之后调用的，但是你也可以在你发布了可观察值之后立即调用它。现在它将立即开始发射元素:

```
var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))  
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish();
somethingsFromWeb.Connect();

somethingsFromWeb  
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });

somethingsFromWeb  
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ }); 
```

`Publish()`和`Connect()`还有一个很好的兄弟姐妹叫做`RefCount()`。`RefCount()`让分享更简单。你可以在`Publish()`之后直接调用它，而不需要调用`Connect()`，`RefCount()`会为你做的。

```
var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))  
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish()
    .RefCount();

somethingsFromWeb  
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });

somethingsFromWeb  
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ }); 
```

`RefCount()`的工作方式是这样的:当“refcounted observable”收到它的第一个订阅者时，它在发布的 observable 上调用`Connect()`。被重新计数的可观察对象然后作为一个定期出版的可观察对象工作，在`Publish()`之前在它的所有订户之间共享一切。

总是记录它有多少用户。当不同的订户停止使用 refcounted observable 时，通过处理订阅，它检查是否还有订户。如果它没有订阅者，它会处理已发布的可观察对象，因为它不再被任何人使用。

现在去修复你的代码ðÿ˜š.

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/dont-do-extra-work-use-publish/)T3】*