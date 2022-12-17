# 为什么是 Kotlin 语言，Android？谷歌为什么选择科特林？

> 原文：<https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639>

## 为什么是科特林语？

如果今天有人问我，Android 应用程序的[开发与其他领域的区别是什么，我会毫不犹豫地回答，在不同硬件的设备上以原生方式执行相同应用程序的可能性是其中之一；但是……这怎么可能呢？今天我想开始我的 Kotlin 语言系列文章，解释这种语言和它的好处。](https://apiumhub.com/mobile-app-development-barcelona/)

在这一点上，没有人会对在任何设备和任何平台(Android、iOS、Windows、MAC OS……)上运行相同的 web 应用程序感到惊讶，我们都知道这些应用程序比任何原生应用程序都更慢、更不稳定；但是作为交换，我们只需要为所有平台开发一个应用程序。当谈到目前运行 Android 的不同设备的数量时，也会出现类似的问题；而我说*会*如果不是因为 Java。Java 的力量和它被用于[今天数十亿台设备](https://skeptics.stackexchange.com/questions/9870/do-3-billion-devices-run-java)的事实，它能够在任何设备上工作，不管它的硬件和软件如何，只要它有一个由 Java 编译器生成的伪编译代码的解释器(官方的 Java 解释器是 Java 虚拟机，尽管在 Android 上 Dalvik 被用于今天的第一个版本和 ART 中)。

这是否意味着 Java 是万恶之源？不幸的是，事实并非如此……尽管 Java 解决了设备间的互操作性问题，但它也带来了一系列令人头疼的问题，我们希望能够消除这些问题，其中包括*:

**注意:虽然这些问题在 Java 8 和 9 中得到了解决，但在 API 24 以下的 Android SDK 中却没有，这使得它们实际上无法使用)*

*   没有对选项的本地支持(尽管我们有对不可变集合的支持)。虽然有*可选的<T>T1】类，但它的使用意味着生成大量的*样板代码*，如果对选项的支持是在语言本身内部构建的，而不是作为它的扩展，我们就可以节省这些代码。*

*   没有对函数式编程的本地支持:在 Java 中有 Stream Api(同样，它只在从 API 24 开始的 Android 中有支持)，但它在语言中的集成类似于 Optional，它很少存在于与原语类型( *IntStream，DoubleStream* …)相关联的对象中，并通过流类< T >用于所有其他对象。

*   支持匿名函数(Lambdas)。虽然 Java 8 加入了对 Lambda 函数的支持，但这些都不是[一等公民](http://thatcsharpguy.com/tv/first-class-citizens/)；这意味着，尽管我们可以使用 lambdas 用单个方法实现匿名接口，但 Java 不支持将函数作为参数传递给方法。

    此外，缺乏类型推理使得 Lambdas 的语句相当不舒服，尤其是在试图模拟函数如函数的复合或 currying 时；在语言上缺乏对它们的支持。

类型可空性:虽然这可以包含在引用可选的部分中，但是这个问题的范围值得特别提及。有多少 Java 程序员没有用 if ( *foo)填充他们的代码！= Null* )试图对抗可怕的 *NullPointerException“十亿美元的错误”*？这些检查中有多少不仅仅是为了避免应用程序崩溃的补丁呢？

手动视图的绑定:虽然这个问题是 Android 平台特有的，而不是 Java 语言特有的，但是也有必要指出获取 Android 视图引用所需的样板代码的数量。尽管由于数据绑定，我们已经设法消除了令人讨厌的 *findViewById (int id)* ,但我们仍然必须存储对该绑定的引用。

它更通用，但同样重要，Java 是一种非常冗长的语言，它需要为任何操作编写大量代码，以及生成大量文件(每个类一个)。第一个问题会导致代码维护成本更高，更容易出错。第二个是阶级扩散的问题。

## 为什么科特林语与这一切决裂？

正是由于所有这些原因，今天，Java 被认为是一种语言，至少在 Android 开发中，没有以业界的速度发展。

久而久之，需要有一种语言来真正和原生地支持上面提到的一切变得更加迫切，以及保持本文开始时暴露的 Android 的主要功能，它的能力，编写和编译单个应用程序，使其在任何设备和版本上工作。在这个方向上，已经探索了许多可能性，其中一些是使用 [Swift](https://academy.realm.io/posts/swift-on-android/) 或 [Scala](http://scala-android.org/) ，尽管没有一个非常有前景。

这一切随着科特林语的出现而改变。Kotlin 是一种由 Jetbrains 设计和开发的语言，致力于成为一种不断发展的现代语言，最重要的是，它可以在 JVM 上执行。这使得它非常适合在 Android 上使用。

为了开始演示，我们可以列出 Java 面临的所有缺点，以及 Kotlin 语言在这些缺点面前的表现:

*   选项。它们内置在 Kotlin 中，你所要做的就是声明一个以问号结尾的变量的类型？所以变成了可选类型。Kotlin 语言还提供了安全解开这些选项的可能性。。onSuccess() 不检查这个可选的是否有值，并且还提供了[猫王操作符](https://kotlinlang.org/docs/reference/null-safety.html#elvis-operator)。

*   函数式编程:在 Kotlin 中，我们找到了处理集合和数据集(如流)的原生支持。我们可以直接打电话。集合中的 flatMap {}，以及。过滤器{}，。地图{}，等等。类型的推断使得 Lambdas 的使用特别容易管理。

*   [Lambdas 和高阶函数](https://kotlinlang.org/docs/reference/lambdas.html):上一点用 Kotlin 语言中函数是一等公民的事实来完成。我们可以定义接收其他函数作为参数的函数。这方面的一个例子是映射函数本身的定义:

```
inline fun <T, R> Iterable.map(transform: (T) -> R): List (source) 
```

虽然乍一看这段代码可能有点混乱，但是让我们感兴趣的是

**变换:(T) - > R**

这意味着，map 函数有一个名为 transform 的参数，它本身是一个函数，有一个 T 类型的输入参数，并返回一个 r 类型的对象。

由于这种对 lambdas 的本地支持，在 Kotlin 语言中，我们可以使用 map 函数:

```
collection.map { item -> aTransformation(item) } 
```

该代码片段将返回由*at transformation*返回的类型的元素集合。

*   类型可空性:在 Kotlin 语言中，由于语言中集成了对可选性的支持，我们应该在代码中尽可能减少可空性的数量。但即便如此，如果它存在的话，Kotlin 为我们提供了比 Java 更容易处理它们的工具。例如我们有操作员 *[安全呼叫(？)](https://kotlinlang.org/docs/reference/null-safety.html#safe-calls)* 来避免访问可选时出现 NullPointerException，或者用运算符 *[安全强制转换](https://kotlinlang.org/docs/reference/typecasts.html#safe-nullable-cast-operator)* 来保护我们以防想要执行强制转换。此外，Kotlin 的编译器强制控制可能具有空值的类型，甚至引入了运行时检查以确保与 Java 代码兼容。

*   视图绑定:这是一个特定的 Android 问题，Jetbrains 为我们提供了 [Kotlin Android 扩展](https://kotlinlang.org/docs/tutorials/android-plugin.html)；一个官方支持库通过一个 gradle 插件来简化这个问题(以及其他一些问题)。

*   语言冗长:

### Java

```
Public interface Listener {
    void success(int result);
    void error(Exception ex);
}

Public void someMethod(Listener listener) {
    int rand = new Random().nextInt();
   If (listener != null) {
        if (rand <= 0) {
            listener.onError(new ValueNotSupportedException());
    }
    else {
            listener.success(rand);
    }
   }

Public void fun(Type1 param1) {
    param1.someMethod(new Listener() {
        @Override
        public void success(int result) {
            println(“Success” + result);
        }

        @Override
        public void error(Exception ex) {
            ex.printStackTrace();
        }
    }
} 
```

### [釜底抽薪](#kotlin)

```
fun someMethod(success: (Int) -> Unit, error: (Exception) -> Unit) {
        val rand = Random().nextInt()
        if (rand <= 0) {
            error(ValueNotSupportedException())
        else {
            success(rand)
        }
} 
```

甚至，使用表达式:

```
fun someMethod(success: (Int) -> Unit, error: (Exception) -> Unit) {
        val rand = Random().nextInt()
        if (rand <= 0) error(ValueNotSupportedException()) else success(rand)
} 
```

由读者决定这两个片段中哪一个更容易编写和解释。

上面讨论的所有内容，以及大量不适合本文或者对我们来说不重要的特性，向我们展示了 Kotlin 语言似乎是未来几年移动开发领域最有前途的赌注。在我的下一篇文章中，我们将更详细地研究在 Android 开发中使用 Kotlin 会给我们带来什么好处，以及它对行业的影响。

如果你对移动开发感兴趣，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

## 如果你觉得这篇关于科特林语的文章很有趣，你可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

帖子[为什么 Kotlin 语言，Android？谷歌为什么选择科特林？](https://apiumhub.com/tech-blog-barcelona/kotlin-language/)最早出现在 [Apiumhub](https://apiumhub.com) 上。