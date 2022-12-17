# Android 上的 Kotlin:实际上，这并不是一个大惊喜

> 原文：<https://dev.to/lovis/kotlin-on-android-not-a-big-surprise-actually>

Google 终于宣布了对 Android 的一流 Kotlin 支持！ðÿž‰ðÿðÿžš
我们中的一些人*知道*这一天会发生，我们现在可以有把握地说“告诉过你！”。 <sup>[1](#fn1)</sup>

然而，并不是所有人都这么肯定。

大约在去年 4 月，一名同事在我们公司的 Slack 上发表了一篇文章[“据说谷歌正在考虑将 Swift 作为 Android 的‘一级’语言”](https://thenextweb.com/dd/2016/04/07/google-facebook-uber-swift/)。
我很好奇，所以马上看了这篇文章。我不仅好奇，而且怀疑。事实证明，我是对的。

虽然 Swift 绝对是一门伟大的语言，但感觉有些不对劲。我想“为什么不是科特林？”。我们这些已经玩过 Kotlin，甚至在生产中使用过它的人都知道它的好处和易用性。

尽管文章的标题如此，但文章很快指出，使用 swift 实际上太复杂了:

> “Android 需要一个 Swift 运行时”
> 
> “谷歌还必须让它的整个标准库做好 Swift 准备”
> 
> “Swift 也无法在 Java 中桥接更高级别的 API”

所以我的怀疑是，作者选择“Swift”作为标题，因为它会比“谷歌考虑 Java 以外的其他语言”产生更多的点击。

我的一些同事(iOS 开发人员，显然是ðÿ˜‰)，在哪里卖反正:

> “我在当地的可可酒吧看到了一个演讲。他向我们展示了如何在 Android 上运行 Swift。”
> “哦，真有意思。他是怎么做到的？”
> “我真的不知道，我想是用土著绑定的东西”
> “他用的是 NDK？那不太方便，不是吗？这也有点慢。”
> “也许吧，但你现在可以在 Android 上使用 Swift 了！所以我们只写一次我们的模型，并在任何地方使用它！还有，等`Foundation`准备好了，你甚至都不再需要`Retrofit`和这些东西了！！！！111!"
> “老兄，督邮甚至知道改造是什么？此外，Kotlin 具有与 Swift 或多或少相同的功能，但是开箱即用……”
> “没关系，雨燕更好！”

好吧，也许我在这里夸大了，对话实际上更加文明，但这就是我记忆中的ðÿ˜œ.关键是，即使在当时，Kotlin 已经是 Android 的更好选择。
Kotlin 一直是以 Android 为核心开发的。在我看来，这是*为 Android 开发而做的*:

*   它编译成 Java 6 兼容的字节码
*   它可以与现有的 Android 和 Java API 100%互操作
*   Android 框架严重依赖于`null`——kot Lin 使得处理它更加安全。
*   像`lazy`或`lateinit`-关键字这样的委托使得与 Android `Resources`类一起工作更加舒适。
*   Lambdas 可以被`inline` d 以获得性能。
*   扩展函数帮助我们在没有反射或子类化的情况下向 Android 框架类添加功能。
*   扩展函数是静态解析的，这也有利于提高性能。
*   Kotlin 运行时(与它的强大相比)真的很小——只有大约 7000 个方法和 1MB。
*   Android 开发者常用的 Dagger、Butterknife、Android Databinding 等库运行流畅，几乎不需要额外配置。
*   很多库已经在考虑 Kotlin(或者为 Kotlin 设计)，例如 [Rx Binding](https://github.com/JakeWharton/RxBinding) 或者 [Anko](https://github.com/Kotlin/anko)
*   您不需要在 Java 和 Kotlin 之间进行选择——您可以在同一个项目中同时使用这两种语言！

文章继续讨论为什么 Kotlin 可能不会成为谷歌的首选语言。

事实上，文章提到的唯一反对 Kotlin 的论点是*“谷歌目前的心态是 Kotlin 在编译时有点太慢了。”*。这对我来说听起来很荒谬:那时我们的 Android 团队最多有 2 分钟的构建时间，而 iOS 努力让他们的构建时间低于 15 分钟。

另一个“论点”对我来说似乎是无知:

> 虽然 Kotlin 是一种替代语言，但它是一种非常新的语言，没有 Swift 那样急切的社区。

我不知道任何看过 Kotlin 的人怎么会称它为“新生的” <sup>[2](#fn2)</sup> (如果作者真的使用了它，那么写这样的东西就更奇怪了)，特别是如果你考虑到在文章发表时它已经在 1.0 中一个多月了。
尽管社区的观点可能是对的，但我怀疑如果苹果自己不接受 Swift，Swift 的社区会如此热切。Swift 是一种非常好的语言，但它不是 Android 开发的正确选择。

在 Google i/o 2017 上，谷歌迈出了正确的一步，将 Kotlin 作为 Android 平台的官方支持语言。每一个害怕 Android 采用 Kotlin 的 CTO 现在都可以放心了:你的开发者知道他们在做什么。

令人惊讶的不是 Kotlin 现在得到了官方支持，真正令人惊讶的是谷歌花了这么长时间才接受它。

* * *

我最初是以“Swift 在 Android 上是不会发生的”的名义起草这篇文章的，但后来谷歌 i/o 越过了我的计划ðÿ˜….这篇文章不应该是咆哮或显示我有多伟大，因为我预料到了这一点。它应该简单地强调为什么科特林是更好的选择。

 我理解这里的“新生”是指“太小而不能当真”，因为它被用作*反对*科特林的论据。

封面图片是由 [JetBrains 作品创作者](https://www.jetbrains.com/goodies/code2art/)创作的