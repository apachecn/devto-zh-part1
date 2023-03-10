# 了解 RxJava 主题—发布、重放、行为和异步主题

> 原文：<https://dev.to/amitiitbhu/understanding-rxjava-subjectpublish-replay-behavior-and-async-subject>

本文是关于 RxJava 中可用的主题。

*   发布主题
*   重播主题
*   行为主体
*   异步主题

由于我们已经有了基于 RxJava2 学习 RxJava 的样例项目(很多开发人员都是从这个样例项目中学习的)，所以我在同一个项目中包含了主题示例。分叉，克隆，构建，运行，学习 RxJava。

[点击这里查看](https://github.com/amitshekhariitbhu/RxJava2-Android-Samples)

那么，让我们来了解一下这个主题。

什么是主体？

> 主体是一种桥梁或代理，在 ReactiveX 的一些实现中可以用作观察者和被观察者。因为它是观察者，所以它可以订阅一个或多个可观测量，因为它是可观测量，所以它可以通过重新发射它们来穿过它所观察的项目，它还可以发射新的项目。

我相信:通过例子学习是最好的学习方法。

**可观察:**假设一个教授是可观察的。这位教授讲授某个话题。

**观察者:**假设一个学生是观察者。学生观察教授所教的主题。

## 发布主题

它发出订阅时可观察到的源的所有后续项目。

在这里，如果学生很晚才进入教室，他只想从他进入教室的那个时间点开始听。因此，发布将是这个用例的最佳选择。

参见下面的例子:

```
PublishSubject<Integer> source = PublishSubject.create();

// It will get 1, 2, 3, 4 and onComplete
source.subscribe(getFirstObserver()); 

source.onNext(1);
source.onNext(2);
source.onNext(3);

// It will get 4 and onComplete for second observer also.
source.subscribe(getSecondObserver());

source.onNext(4);
source.onComplete(); 
```

Enter fullscreen mode Exit fullscreen mode

点击此处查看完整示例。

## 重播主题

它发出源可观察的所有项目，不管订户何时订阅。

在这里，如果一个学生很晚才进教室，他想从头开始听。因此，这里我们将使用重放来实现这一点。

参见下面的例子:

```
ReplaySubject<Integer> source = ReplaySubject.create();
// It will get 1, 2, 3, 4
source.subscribe(getFirstObserver()); 
source.onNext(1);
source.onNext(2);
source.onNext(3);
source.onNext(4);
source.onComplete();
// It will also get 1, 2, 3, 4 as we have used replay Subject
source.subscribe(getSecondObserver()); 
```

Enter fullscreen mode Exit fullscreen mode

点击此处查看完整示例。

## 行为主体

当观察者订阅它时，它发出最近发出的项目和源可观察的所有后续项目。

在这里，如果一个学生很晚才进教室，他想听教授讲的最近的事情(不是从头开始)，这样他就能理解上下文。因此，这里我们将使用行为。

参见下面的例子:

```
BehaviorSubject<Integer> source = BehaviorSubject.create();
// It will get 1, 2, 3, 4 and onComplete
source.subscribe(getFirstObserver());
source.onNext(1);
source.onNext(2);
source.onNext(3);
// It will get 3(last emitted)and 4(subsequent item) and onComplete
source.subscribe(getSecondObserver());
source.onNext(4);
source.onComplete(); 
```

Enter fullscreen mode Exit fullscreen mode

点击此处查看完整示例。

## 异步主题

它只发出源可观测值的最后一个值(并且只有最后一个值)

在这里，如果一个学生在任何时间点进入教室，他只想听最后一件(也只有最后一件)被教的东西。因此，这里我们将使用异步。

参见下面的例子:

```
AsyncSubject<Integer> source = AsyncSubject.create();
// It will get only 4 and onComplete
source.subscribe(getFirstObserver());
source.onNext(1);
source.onNext(2);
source.onNext(3);
// It will also get only get 4 and onComplete
source.subscribe(getSecondObserver());
source.onNext(4);
source.onComplete(); 
```

Enter fullscreen mode Exit fullscreen mode

点击此处查看完整示例。

因此，无论何时你遇到这种情况，RxJava 主题都将是你最好的朋友。

快乐编码:)

准备安卓面试:[安卓面试问题](https://github.com/amitshekhariitbhu/android-interview-questions)

[阿米特谢柯尔](https://amitshekhar.me)

*原贴[此处](https://amitshekhar.me/blog/rxjava-subject-publish-replay-behavior-async)。*