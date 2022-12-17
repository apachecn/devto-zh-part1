# Kotlin 的 RxJava 组合测试快速示例

> 原文：<https://dev.to/amay077/rxjava-combinelatest-quick-example-e8b>

RxJava 的`combineLatest`非常方便，但是我很难使用它，因为它有很多重载。
所以我写了`combineLatest`的代码片段。

```
val name = PublishSubject.create<String>()
val age = PublishSubject.create<Int>()

// Can not omit Type parameters and BiFunction 
Observable.combineLatest<String, Int, String>(
        name, age, BiFunction { n, a -> "$n - age:${a}" })
        .subscribe({
            Log.d("combineLatest", "onNext - ${it}")
        })

// If you introduce RxKotlin then you can use type inference
Observables.combineLatest(name, age) { n, a -> "$n - age:${a}" }
        .subscribe({
            Log.d("combineLatest", "onNext - ${it}")
        })

// Also we can use Observable array for 1st parameter 
// but second parameter to be array, it's not cool.
Observable.combineLatest(arrayOf(name, age), {
    val n = it[0] as String
    val a = it[1] as Int
    "$n - age:${a}" })
        .subscribe({
            Log.d("combineLatest", "onNext - ${it}")
        })

name.onNext("saito")
age.onNext(24)
name.onNext("yoshida") 
```

Enter fullscreen mode Exit fullscreen mode

*   [react vex/rx Java:rx Java——JVM 的反应式扩展——一个使用 Java VM 的可观察序列组成异步和基于事件的程序的库。](https://github.com/ReactiveX/RxJava)
*   [react vex/RxKotlin:Kotlin 的 RxJava 绑定](https://github.com/ReactiveX/RxKotlin)