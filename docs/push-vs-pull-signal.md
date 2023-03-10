# 推 vs 拉信号

> 原文：<https://dev.to/onmyway133/push-vs-pull-signal>

原帖[https://github.com/onmyway133/blog/issues/28](https://github.com/onmyway133/blog/issues/28)

信号的想法可能源于 [Elm 反应](http://elm-lang.org/guide/reactivity)，现在已经被 iOS 广泛采用

我曾经问过[reactive cocoa 中有哪些冷热信号的例子？](http://stackoverflow.com/questions/29374792/what-are-examples-of-hot-and-cold-signal-in-reactivecocoa)

*   [什么时候使用 IEnumerable vs IObservable？](http://stackoverflow.com/questions/17082255/when-to-use-ienumerable-vs-iobservable)
*   [ReactiveCocoa 框架概述](https://dev.toFramework%20Overview)
*   [Rx–适用于初学者(第 9 部分):可观察到的冷热对比](http://blogs.microsoft.co.il/bnaya/2010/03/13/rx-for-beginners-part-9-hot-vs-cold-observable/)
*   [RxSwift 冷热观察值](https://github.com/ReactiveX/RxSwift/blob/master/Documentation/HotAndColdObservables.md)

无论是热 vs 冷，信号 vs 信号生产者，可观测 vs 可枚举，...理解它是如何实现的是很好的，这样才能很好地理解它们是如何工作的

## 单子

*   [理解单子](http://www.fantageek.com/blog/2015/07/10/understanding-monad/)

基本上，信号及其结果只是单子，是可以映射和链接东西。

Signal 利用了延迟执行回调块，而`push vs pull`就是信号更新其值和调用回调的顺序的方式

执行回调块是我们将一个函数传递给另一个函数，它将在被调用时被调用

## 同步与异步

Monad 可以处于同步或异步模式。Sync 更容易理解，但 async 是您已经熟悉并在实践中使用的

基本上，

*   同步:你通过`return`立即得到返回值
*   Aync:您通过回调块获得返回值

下面是一个简单函数的例子

```
// Sync
func sum(a: Int, b: Int) -> Int {
    return a + b
}

// Async
func sum(a: Int, b: Int, completion: Int -> Void) {
    // Assumed it is a very long task to get the result
    let result = a + b

    completion(result)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个`Event`
的例子

```
// Sync
public func map<U>(@noescape f: T -> U) -> Event<U> {
    switch self {
    case let .Next(value):
        return .Next(value: f(value))
    case let .Failed(error):
        return .Failed(error: error)
    }
}

// Async
public func map<U>(f: (T, U -> Void) -> Void) -> ((Event<U> -> Void) -> Void) {
        return { g in   // g: Event<U> -> Void
            switch self {
            case let .Next(value):
                f(value) { transformedValue in  // transformedValue: U
                    g(.Next(value: transformedValue))
                }
            case let .Failed(error):
                g(.Failed(error: error))
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 推送信号

*   [Signal.swift](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Signal.swift)
*   [UIKonf 2015 - Jens Ravens:没有黑魔法的函数式反应式编程](https://www.youtube.com/watch?v=AcDaWe3S75c)

看看我的推送信号，叫做[信号](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Signal.swift)，它就像[承诺 A+然后](https://github.com/onmyway133/Then/blob/master/Pod/Classes/Promise.swift)是如何工作的

### 实现

```
public final class Signal<T> {
    var event: Event<T>?
    var callbacks: [Event<T> -> Void] = []

    func notify() {
        guard let event = event else {
            return
        }

        callbacks.forEach { callback in
            callback(event)
        }
    }

    func update(event event: Event<T>) {
        dispatch_sync(lockQueue) {
            self.event = event
        }

        notify()
    }

    public func subscribe(f: Event<T> -> Void) -> Signal<T> {
        // Callback
        if let event = event {
            f(event)
        }

        callbacks.append(f)

        return self
    }

    public func map<U>(f: T -> U) -> Signal<U> {
        let signal = Signal<U>()

        subscribe { event in
            signal.update(event: event.map(f))
        }

        return signal
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
let signal = Signal<String>()

signal.map { value in
        return value.characters.count
    }.subscribe { event in
        if case let .Next(value) = event {
            XCTAssert(value == 4)
        } else {
            XCTAssert(false)
        }
}

signal.sendNext("test") 
```

Enter fullscreen mode Exit fullscreen mode

### 回调

给定像这样的连锁信号

A -(地图)-> B -(平面地图)-> C -(平面地图)-> D -(订阅)

*   这个想法是我们发送事件到源信号，它通过回调传播事件。
*   由发送事件到源信号触发。
*   我们必须留住一个人，因为它能留住其他人
*   我们订阅最后一个 D
*   我们将事件发送给第一个 A
*   A 的回调被调用，它又用 A 的映射结果调用 B 的回调，然后 B 的回调用 B 的平面映射结果调用 C 的回调，...

## 拉信号

*   [Future.swift](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Future.swift)
*   [Swift 同步和异步错误处理- iOS Conf SG 2015](https://www.youtube.com/watch?v=mbd6g7NfR-8)

看看我的拉动信号，叫[未来](https://github.com/onmyway133/Signal/blob/master/Pod/Classes/Future.swift)

### 实现

这里的`operation`是一个任务，当被调用并完成后，`completion`会通知其

```
public struct Future<T> {
    let operation: (Event<T> -> Void) -> Void

    public init(operation: (Event<T> -> Void) -> Void) {
        self.operation = operation
    }

    public func start(completion: Event<T> -> Void) {
        operation() { event in
            completion(event)
        }
    }

    public func map<U>(f: T -> U) -> Future<U> {
        return Future<U> { completion in
            self.start { event in
                completion(event.map(f))
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
let _ = Future<String> { completion in
        // There is some work here
        completion(Event(value: "test"))
    }
    .map { value in
        value.characters.count
    }.start { event in
        if case let .Next(value) = event {
            XCTAssert(value == 4)
        } else {
            XCTAssert(false)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 回调

给定像这样的连锁信号

A -(地图)-> B -(平面地图)-> C -(平面地图)-> D -(订阅)

*   这个想法是我们同意最后的信号 D，它导致前面的信号动作。
*   通过订阅最终信号来触发。
*   我们必须留住 D，因为它能留住其他人
*   我们订阅最后一个 D
*   d 的操作动作，并导致 C 的操作动作，...然后 A 的操作动作。任务是在 A 中执行的(如获取网络、检索数据库、文件访问、繁重的计算，...)来获得结果，并调用 A 的完成。然后 A 的完成用 B 的映射所映射的结果调用 B 的完成，...一直到用户的完成模块