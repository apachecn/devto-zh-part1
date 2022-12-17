# Android 开发的 Kotlin:重量级语言还是无望的炒作？

> 原文：<https://dev.to/bugfenderapp/kotlin-for-android-development-heavyweight-language-or-hopeless-hype>

在我们的[博客](https://bugfender.com/blog/kotlin-for-android-development-heavyweight-language-or-hopeless-hype/)上阅读这篇文章。

在谷歌的 I/O 主题演讲上，我们了解到, [Kotlin 编程语言](https://kotlinlang.org/)将在 Android 上得到官方支持。世界上成千上万的开发人员互相击掌庆祝——许多人认为 Java 作为一种语言已经相当过时了(不可否认，我是一个 Java 爱好者)。

对科特林的炒作有道理吗？Swift 同样令人兴奋，但许多人认为这种语言还没有准备好，因此还没有使用它。

两年前，我发誓在谷歌宣布支持 Kotlin 之前，我不会开始认真学习 kot Lin。他们现在已经做到了——而我正在学习。

我想快速学习这种新语言，所以我集中了我所有的超级开发能力，并借鉴了我已经熟悉的语言(Java 和 Scala)。

## 第一印象

我在阅读 Kotlin 文档时注意到的第一件事是，它编译字节码、JavaScript 和 Native，并且是由 [Jetbrains](https://www.jetbrains.com/) 开发的(我是它的忠实粉丝)。我们来分析一下。

如果 Kotlin 编译成字节码，这意味着它在运行时使用 JVM (Java 虚拟机)将字节码编译成本机代码。

JavaScript 呢？基本上，它是用目标 ECMAScript 5.1 将 Kotlin 代码转换成 JavaScript。不过，我不是 JavaScript 专家——查看[官方文档](https://kotlinlang.org/docs/reference/js-overview.html)了解更多详细信息。

Kotlin 的一个明智之举是能够编译本机，并在没有 JVM 的情况下支持更多平台——现在 Kotlin Native 可以针对 Mac OS、Linux、Raspberry Pi 和 iOS(通过在 Mac 上交叉编译),使用 LLVM 生成可执行文件。还不支持 Windows，但他们正在努力。

我的梦想场景:用 Kotlin Native 开发我的 Android 和 iOS 应用的所有业务逻辑，然后用特定平台语言(Android = Java / Kotlin，iOS = Objective-C / Swift)开发 UI。Kotlin Native 还年轻，所以我必须等待才能做到这一点。

另一个紧迫的问题是:

Kotlin 会 100%兼容 Java 吗？

只需看看主页就能找到答案:

[![](img/3fdd250e4c41960dbb170b0e79cd03fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PIwZYWF5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/06/kotlin-blog-1.png)

以下是我的反应:

[![](img/fa3be30733de90fbb6ed0404333e3e7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ulEOvYOC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://bugfender.com/wp-content/uploads/2017/06/kotlin-blog-2.gif)

但是在我们真正开始行动之前，有必要更详细地探索一下。

如果你在网上读到科特林，人们会这么说:

*   不变
*   代码比 Java 少
*   零安全
*   Java 互操作
*   函数式编程

让我们详细讨论一下这些问题，看看它们是否站得住脚。

## 不变

与所有具有函数式编程概念的语言一样，不可变性是主要讨论点之一。在科特林是怎么运作的？

变量可以是:

*   **可变**:用关键字 **var** 表示
*   **不可变**:由关键字 **val** 表示

关键词 **val** 真的不可改变吗？

没有。在定义纯不变性时，我们总是需要检查它是否满足两种类型的不变性:

*   **不可变引用**:引用一旦被赋值，就不能再赋给别的。
*   **不可变值**:引用的值不能变异。

```
fun foo() {
    var mutable: Int = 1
    val immutable: Int = 2

    mutable = 2 // All OK
    immutable = 3 // Compile error

    val collection = arrayListOf(1, 2, 3) // Immutable reference to a collection
    collection.add(4) // Adding a value to the collection, so we are modifying the values of the immutable collection.
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，科特林有不可变的集合吗？

是的，确实如此。

您可以在不可变的集合、映射、集合等之间进行选择。，或者不是，因为该语言有两种实现。这里有一个例子:

```
fun bar() {
    val immutableList = listOf(1, 2, 3) // Immutable by reference and value
    val mutableList = mutableListOf(1, 2, 3) // Immutable by reference and not value
} 
```

Enter fullscreen mode Exit fullscreen mode

## 代码比 Java 少

Kotlin 有只保存数据的数据类，所以这是一种编写所有 POJO 类的干净方法。

在 Java 中:

```
public class Foo {
    private int id;
    private String name;

    public Foo(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在科特林:

```
data class Foo(var id: Int, var name: String) 
```

Enter fullscreen mode Exit fullscreen mode

好了，我能听到你在想:为什么用 var(可变)而不用 val(不可变)？

是因为我把 Java 里的 Foo 类做成了可变的。

另一种编写更少代码的方法可以在 Kotlin 扩展中找到。它们允许你在不修改源代码的情况下向类中添加方法/函数——告别 Utils 类。Kotlin 团队使用了 JDK 类的扩展，如文件、IO 和线程。

[![](img/d1dbc944e8be47bcb3d69086436db26b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fh7YFD1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://bugfender.com/wp-content/uploads/2017/06/kotlin-blog-3.gif)

这里有一个来自标准库的例子:

```
fun File.deleteRecursively(): Boolean 
```

Enter fullscreen mode Exit fullscreen mode

```
fun File.forEachLine(
    charset: Charset = Charsets.UTF_8, 
    action: (line: String) -> Unit) 
```

Enter fullscreen mode Exit fullscreen mode

## 零安全

如果你用 Java 开发，在某些时候你将不得不处理 NPE(空指针异常)，当你忘记检查一个变量是否为空或者你不希望一个空变量出现时，它就会出现。

Kotlin 有一个聪明的解决办法:默认情况下，使所有类型不可为空。因此，编译器不会让你使用未初始化或不可空的变量。如果你愿意，你仍然可以使用可空类型，通过使用操作符“**？**”。

让我给你看几个例子

```
var foo = "Foo"
foo = null // Compile error

var bar: String? = "Bar"
bar = null 
```

Enter fullscreen mode Exit fullscreen mode

您可能会想，使用不可空的类型并不总是可能的，因为有时这超出了您的控制范围——例如，网络响应问题。

别担心。Kotlin 包含一个“Elvis 操作符”，通过它我们可以用这个简单的操作符“**”来表达典型的 if else null 检查。**:

```
val foo: String = bar ?: "Foo" 
```

Enter fullscreen mode Exit fullscreen mode

所以再见 **if else** 表情——不会有人想念你的。

```
String foo = nullableResponse();

if (foo == null) {
     // do something
} else {
     // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

最后要提到的至关重要的一点是，你可以使用运算符“**！！**“与算子一样”**？**”。区别？如果变量为空，它会抛出一个 NullPointerException，所以请避免使用它！

[![](img/e564573460b72a1dd9221096e41ea386.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ijfyh3-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://bugfender.com/wp-content/uploads/2017/06/kotlin-blog-4.gif)

## Java 互操作

这个话题对我来说特别重要，因为我所有的 Android 或后端库都是用 Java 编写的。我也为他们感到骄傲，所以我现在不想改变他们。

我之前说过，Kotlin 是 100%兼容 Java 的。

下面是一些使用 GSON 和改装的例子:

```
@GET("playlistItems")
fun playlistItems(@Query("part") part: String,
     @Query("maxResults") maxResult: Int, @Query("playlistId") playlistId: String,
     @Query("pageToken") pageToken: String?, @Query("key") key: String): Call<VideoResponseNetwork> 
```

Enter fullscreen mode Exit fullscreen mode

```
val response: Response<VideoResponseNetwork> = api.playlistItems(api.SNIPPET_PART, s.limit, s.playlistId, s.nextPageToken, googleApiKey).execute() 
```

Enter fullscreen mode Exit fullscreen mode

```
data class VideoResourceNetwork(@SerializedName("kind") val kind: String?,
     @SerializedName("videoId") val videoId: String?) : Model(identifier = videoId) 
```

Enter fullscreen mode Exit fullscreen mode

在 Mobile Jazz，我们开发了 [Bugfender](https://bugfender.com/) 作为 iOS 和 Android 应用程序的远程日志服务。Android SDK 完全是用 Java 开发的。Kotlin 可以处理这一点——由于 Java 互操作，它可以用于您的 Kotlin Android 应用程序中。[点击此处查看示例](https://github.com/bugfender/BugfenderSDK-Kotlin-sample)。

我想分享的还有很多，但 Kotlin 文档是我们读过的最好的文档之一。所以，如果你想了解更多，就过来吧。

## 结论

相信宣传——kot Lin 是一种成熟的语言，它给 Java 开发人员一个现代语言的新开始。Java 8 比以前的 Java 版本有了很大的进步，但是作为 Android 开发者，我们只能使用 Java 8 的一小部分功能。因此，科特林对我们来说更好。

你试过科特林吗？我们的概述错过了目标吗？联系我们，让我们知道你的想法。