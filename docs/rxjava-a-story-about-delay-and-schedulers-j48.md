# RxJava，关于延迟和调度器的故事

> 原文：<https://dev.to/dbottillo/rxjava-a-story-about-delay-and-schedulers-j48>

RxJava 是个畜生。它有很高的学习曲线，但它让你用很少几行代码做复杂的事情，这是有代价的:你需要了解内部是如何工作的，否则它会狠狠地咬你一口！

最近，我发现延迟算子有一个非常奇怪的行为。

让我们假设有一个我们希望在它结束之前延迟的可观测值:

```
Single.fromCallable {
   Thread.sleep(DELAY)
   "done!"
} 
.subscribeOn(Schedulers.io())
.observeOn(AndroidSchedulers.mainThread())
.doOnSubscribe {
   view.showText("started!")
}
.doOnSuccess {
   view.showText(it)
}.subscribe() 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么奇怪的，我们有一个可调用函数，它会在返回“done！”字符串。

但是我们可以用延迟操作符写一个更好的版本:

```
Single.fromCallable {
   "done!"
}
.observeOn(AndroidSchedulers.mainThread())
.subscribeOn(Schedulers.io())
.delay(DELAY, TimeUnit.MILLISECONDS)
.doOnSubscribe {
   view.showText("started!")
}.doOnSuccess {
   view.showText(it)
}
.subscribe() 
```

Enter fullscreen mode Exit fullscreen mode

似乎是合法的，我们已经从 Callable 中移除了丑陋的 Thread.sleep()，现在我们使用延迟操作符，很好！

但是如果你运行这个代码，它会严重崩溃

```
io.reactivex.exceptions.OnErrorNotImplementedException: 
Only the original thread that created a view hierarchy can touch its views. 
```

Enter fullscreen mode Exit fullscreen mode

等等什么？似乎是试图在错误的线程中更新视图！在这种情况下，我通常会查看文档，但这并不是很有帮助:

[http://reactivex.io/documentation/operators/delay.html](http://reactivex.io/documentation/operators/delay.html%C2%A0)

它没有提到任何关于线程的内容。但是你能做的是进入实现的内部！

因此，如果你按照代码中的延迟操作符，你将得到:

```
/**
* Delays the emission of the success signal from the current Single by the specified amount.
* An error signal will not be delayed.
*
* Scheduler:
* {@code delay} operates by default on the {@code computation} {@link Scheduler}.
* 
* @param time the amount of time the success signal should be delayed for
* @param unit the time unit
* @return the new Single instance
* @since 2.0
*/
@CheckReturnValue
@SchedulerSupport(SchedulerSupport.COMPUTATION)
public final Single delay(long time, TimeUnit unit) {
    return delay(time, unit, Schedulers.computation(), false);
} 
```

Enter fullscreen mode Exit fullscreen mode

等等，它说默认情况下它在计算调度器上运行！就是这样，delay 将调度器改回计算，所以我们试图从错误的线程更新视图。

幸运的是，我们可以使用另一种方法:

```
public final Single<T> delay(final long time, final TimeUnit unit, final Scheduler scheduler) 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们可以使用这个可选的延迟操作符来重写代码:

```
Single.fromCallable {
   "done!"
}
.subscribeOn(Schedulers.io())
.delay(DELAY, TimeUnit.MILLISECONDS, AndroidSchedulers.mainThread())
.doOnSubscribe {
   view.showText("started!")
}
.doOnSuccess {
   view.showText(it)
}
.subscribe() 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到，现在您不再需要指定 ObserveOn，因为我们在 delay 操作符中选择了线程。

你可以在我的 github 上找到一个工作示例:[https://github.com/dbottillo/Blog/blob/rxjava_delay](https://github.com/dbottillo/Blog/blob/rxjava_delay)

Happy Rxjava!