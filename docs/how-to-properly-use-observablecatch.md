# 如何正确使用可观测量？Catch()

> 原文：<https://dev.to/jonstodle/how-to-properly-use-observablecatch>

*这是我的博客原帖:[https://blog . jonstodle . com/how-to-rightly-use-observable-catch/](https://blog.jonstodle.com/how-to-properly-use-observable-catch/)*

当对[反应式扩展](http://reactivex.io/)不熟悉时，有很多东西需要理解，很多事情令人困惑。过了一段时间，你开始进入状态，事情会变得更好。但是，突然之间，当你试图使用 [Observable 时，你被卡住了。Catch()](http://reactivex.io/) 。

每当一个异常在一个可观察的链中被抛出，它就会折叠整个链。这意味着，如果可观察对象的来源是一个事件，并且该事件继续发生，那么你的可观察对象没有在听它。

“啊哈！”你可能会想，“我只要在里面放一个 Catch()就解决了”。所以你试着这样做:

```
Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb()) // .flatMap() in non-C# Rx
    .Catch(Observable.Return("")) .Subscribe(x => { /* Do something */ }); 
```

但这并不能解决你的问题。操作符只替换以前的可观察值，它不会以任何方式保存或恢复它。该活动的原始订阅已不存在。

你想做的其实是这样的:

```
Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb().Catch(Observable.Return("")))
    .Subscribe(x => { /* Do something */ }); 
```

但是这有什么不同呢？

记住`Catch()`替换了抛出异常的可观察对象。当有一个 excption 时，你不想替换以`Observable.FromEventPattern()`开头的可观察结果，你想替换从 web 加载失败的结果。

通过将`Catch()`放在`LoadSomethingFromTheWeb()` observable 上，你是在说“从 web 上加载一些东西，然后用 observable 返回结果。如果失败了，就用这个新序列完全取代那个可观测的序列。”

在上面的例子中，我们用一个只返回一个值的新的可观察对象替换了失败的可观察对象:一个空字符串。

虽然`SelectMany`看起来像是返回到它内部的可观察对象消失在父可观察对象中，但它实际上仍然在那里。`LoadSomethingFromTheWeb()`可观察对象仍然存在，只是它的所有项目都直接传递给了父可观察对象。

理解`Catch()`是如何工作的将会为你省去很多麻烦，并使处理你的可观察管道中的任何错误或异常变得容易得多。如果你有任何问题，请在下面留言，我很乐意回答ðÿ˜š.

编码快乐！