# 函数式编程的好处是什么？

> 原文：<https://dev.to/kspeakman/what-is-the-benefit-of-functional-programming>

*在[巴勃罗·里维拉](https://dev.to/yelluw)的建议下发布。*

我最近看到一些帖子试图解决或警告 F#中的函数式编程(FP)模式。所以，我想我应该后退一步，解释如何从 FP 中获得切实的好处，这些好处是什么，以及功能模式如何与之相关。

> 对于功能模式，我指的是部分应用、currying、不变性、联合类型(例如，选项、结果)、记录类型等。

从 FP 中获益要遵循的最重要的原则是编写纯函数。“等等，”我听到你说，“只有纯功能是做不出有用的 app 的。”你是对的。在某些时候，您必须执行一些副作用，比如询问当前时间或者将一些内容保存到数据库中。FP(在 F#中)学习过程的一部分是了解哪里会产生副作用。接下来的旅程是如何将副作用尽可能地从[移到程序的边缘](http://blog.ploeh.dk/2017/02/02/dependency-rejection/)，以最大化内部纯函数的数量。目标是每个重要的决策都由纯函数做出。

“纯函数有什么了不起的？”一个普遍吹捧的好处是可测试性，这几乎是由定义而来的。但根据我的经验，事情远不止如此。基本上是纯功能的程序往往需要较少的时间来维护。换句话说，纯函数允许我无畏地重构它们。由于它们的确定性和类型安全的结合，编译器可以完成大部分的艰苦工作，确保我没有犯严重的错误。纯函数当然不能防止逻辑错误(对我来说最常见的错误是:错误地否定一个布尔值)。但它确实避免了我过去处理过的许多难以追踪和繁琐的问题。

“我明白你的意思，但我持怀疑态度。”是的，你应该害怕。首先，有一个权衡。其次，我不指望你会相信我的话。可能最大的权衡是不可变数据结构。您不是改变数据(例如，更新输入数组中的值，这会给调用者带来副作用)，而是返回具有不同值的数据的新副本。可以想象，复制数据比就地更新花费更多(CPU/内存)。总的来说，我发现没有足够大的差别来担心它。但在某些情况下，性能可能会压倒其他问题。F#的一个好处是，如果需要的话，您可以很容易地切换到执行变异。

至于不相信我的维护/重构好处的话，我可以告诉你我是如何发现这一点的，并建议你尝试一下。我不得不承认，我没有在 F#中深入发现这些好处。因为 F#是多范例的，包括面向对象，所以很容易跳过或者只部分地学习如何用纯函数建模。面对如此多的可用途径，人们也很容易对如何去做感到困惑。我的发现之路必须归功于 [Elm](http://elm-lang.org/) -一种相对年轻的编译 javascript 语言/平台，也是 Redux 的灵感来源。Elm 是一种函数式语言，在这种语言中，你只能使用不可变类型和纯函数(尽管你可以以可控的方式调用 Javascript)。起初，这似乎是限制性的，有点令人费解。

因为不熟悉 Elm 的限制，所以我用 Elm 写的第一个代码并不是最棒的，即使用 F#编码了好几年也是如此。(公平地说，我的大部分 F#代码都是后端代码，包括执行副作用。)我的 Elm 代码可以工作，但是它不太合适或者看起来很复杂。但是当我发现不同的建模方法并尝试它们时，我发现 Elm 实际上非常适合重构，甚至是大规模的重构。当我学习在纯函数环境中建模的新方法时，Elm 对我的代码的约束实际上允许我的代码随我一起轻松增长。大型重构仍然需要一些工作，但是我的经验是，这不再是不可能计划的大量“危险”工作。这只是正常的工作。我不是唯一有这种经历的人。

最终，我明白了这并不是因为 Evan Czaplicki 编织到 Elm 中的一些白色魔法，而是因为所有用户代码都由纯函数组成的有见地的设计选择。纯函数是编译器可以验证的契约。契约并没有详细说明函数的所有内容，但是它可以帮助你在编译时解决大多数真正令人厌烦的问题。

因此，这就是功能模式发挥作用的地方。它们是帮助确保编译器能够验证纯函数契约的策略。我将快速浏览一些例子。

*   开着。至少在. NET 中， [null 没有类型](http://stackoverflow.com/questions/930147/c-sharp-get-type-of-null-object),所以如果你提供一个 null 值，编译器不能验证契约是否被履行。所以有了 F#的选项(可能在 Elm 中)，它不仅保留了类型，还让您知道应该处理“无值”的情况。现在合同又可以验证了。

*   抛出异常会使纯函数的契约失效，因为异常是未声明的隐藏返回路径。输入 Result***，它提供了一种以声明方式返回成功值或错误值的方法，因此可以再次验证协定。

*   在函数声明中不能观察或考虑突变(就地修改数据)。这也是一些很难发现的错误的来源。所以输入默认情况下不可变的记录。要“更改”数据，您必须用不同的值制作记录的副本。现在合同又可以验证了。

因为提供“契约”的基本原语是一个函数，所以局部应用和 currying 自然会带来便利。像 Option 和 Result 这样的类型是可能的(以一种不尴尬的方式),因为联合类型，也就是代数数据类型。你可以建立自己的工会类型。它们非常适合用自己的数据集对互斥案例进行建模。比如 PaymentType 是带卡数据的信用卡或带#的支票。如果你用一个普通的类来建模，这两种情况下你都可能有可空的属性。这就有可能出现一个、两个或一个都不存在，这就需要您做更多的工作来确保数据是有效的。有了联合类型，编译器可以为您做一些工作。

我希望你能看到函数模式主要是为了支持编写纯函数而存在的。你不能完全用纯函数来编写一个有用的程序，所以目标应该是最大化它们的使用，以便最大化可重构性。学习如何做到这一点是一个过程。如果你需要使用不纯的溶液，不要烦恼，直到你发现另一种方法。还要意识到用 F#编写纯函数需要一点训练，因为语言并没有提供一种强制执行的方法。我生成纯函数遵循的基本准则是 1)不改变输入值，2)结果是确定的...也就是说:给定相同的输入，输出总是相同的。我还倾向于为我的程序建立一个外部边界，在那里副作用(数据库操作、HTTP 调用、当前时间等)会被删除。)是允许的。从这个边界我可以调用副作用函数和纯函数。但是纯函数不应该调用副作用函数。到目前为止，这在我的 F#代码中运行得非常好。但我肯定我还有更多要学的。:)

****明智地使用结果。因为异常是。NET 中，几乎所有常见的外部库都会抛出异常。您将为自己创建大量的工作，试图将与外部库的每个交互转换成一个结果。我的经验是，当我必须对一个会抛出的外部库执行多个操作时，就让我的代码抛出。在程序的更高层，我将把代码中的异常转换成结果错误。我还将结果用于业务逻辑失败，如验证错误。*