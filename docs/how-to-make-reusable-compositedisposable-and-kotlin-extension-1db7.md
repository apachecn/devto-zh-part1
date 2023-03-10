# 如何使可重用复合可丢弃和 Kotlin 扩展

> 原文：<https://dev.to/ryugoo/how-to-make-reusable-compositedisposable-and-kotlin-extension-1db7>

## 准备

### app/build.gradle

```
ext {
  rxjava2_version = "2.1.7"
}
dependencies {
  implementation "io.reactivex.rxjava2:rxjava:${rxjava2_version}"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 制造“一次性机器人”

#### 编辑

**使用“CompositeDisposable # clear”**可以获得相同的效果

`CompositeDisposable`已经处理的不能再用。如果你想同步一次性生命周期和 Android 活动生命周期，可以通过制作简单的包装器来实现。

```
class AndroidDisposable {
    private var compositeDisposable: CompositeDisposable? = null

    fun add(disposable: Disposable) {
        if (compositeDisposable == null) {
            compositeDisposable = CompositeDisposable()
        }
        compositeDisposable?.add(disposable)
    }

    fun dispose() {
        compositeDisposable?.dispose()
        compositeDisposable = null
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 如何使用

```
class MainActivity : AppCompatActivity() {
    private disposable = AndroidDisposable()

    override fun onStart() {
        super.onStart()
        disposable.add(/* Some disposable */)
    }

    override fun onStop() {
        disposable.dispose()
        super.onStop()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 科特林分机

Kotlin 可以为现有的类添加新的函数/方法(实体是一个静态方法，它将目标类的实例作为第一个参数)。我将扩展`Disposable`并使其易于使用`AndroidDisposable`。

```
operator fun AndroidDisposable.plusAssign(disposable: Disposable) {
    add(disposable)
}

fun Disposable.addTo(androidDisposable: AndroidDisposable): Disposable
    = apply { androidDisposable.add(this) } 
```

Enter fullscreen mode Exit fullscreen mode

#### 如何使用

```
val texts = listOf("1", "2", "3")

// Operator
disposable += Observable.fromIterable(texts)
    .map { it.toInt(10) }
    .reduce { t1, t2 -> t1 + t2 }
    .subscribe()

// or
Observable.fromIterable(texts)
    .map { it.toInt(10) }
    .reduce { t1, t2 -> t1 + t2 }
    .subscribe()
    .addTo(disposable) 
```

Enter fullscreen mode Exit fullscreen mode