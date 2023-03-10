# 科特林-第一印象

> 原文：<https://dev.to/dev_trent/kotlin---first-impression-5f7a>

*来自[特伦特的开发博客](https://trent-dev.github.io/)的交叉帖子。*

自从我开始一个完全用 Kotlin 写的新项目已经 3 周了。这三周太棒了。我可以从我一直喜欢的严格静态类型的 Scala 经验中享受函数式编程。所有这些，都不会影响工作效率。我一直在使用的 Kotlin 优秀特性有:

*   [数据类别](https://kotlinlang.org/docs/reference/data-classes.html)

只需一行代码就可以创建不可变的值类。Java 也可以做到这一点，但是 Java 这样使用它几乎太冗长了。这对于声明具有少量值的自定义数据类型非常有用。我主要用它来增强某些基本类型值集的静态类型。可以对几乎所有内容进行编译时类型检查，同时通过更少的易错变量处理使代码更简洁、可读性更好。[析构声明](https://kotlinlang.org/docs/reference/multi-declarations.html)使用起来更加简单方便。

*   [扩展功能](https://kotlinlang.org/docs/reference/extensions.html)

与 Java 相比，扩展函数确实让 Kotlin 与众不同。它几乎太强大了，但我发现自己一直在使用它。Android 中你希望库已经拥有的所有方法——无论是简单地开始一个活动还是用特定参数打开一个相机——都在你的指尖，这样你就可以以任何你想要的方式定制框架(或语言)。但是注意不要在方法中包含任何外部状态(变量)。这可能会使程序变得不必要的复杂(这类似于使用单例来存储状态)。如果您只是为了语法上的好处而使用扩展函数，我建议您仔细决定方法的范围。

*   高阶函数([、`let`、`apply`、`run`、`use`等)。](https://medium.com/@tpolansk/the-difference-between-kotlins-functions-let-apply-with-run-and-else-ca51a4c696b8))

像`let`、`apply`、`run`、`use`这样的函数对于保持代码清晰是非常有价值的。它们有助于将过程代码分成对变量执行的操作块。这非常有用，因为即使使用扩展函数，将所有顺序操作隐藏到一个单独的函数中也是不切实际的。在这方面，`apply`非常有趣，因为它被设计成将变量赋值与自定义初始化相结合。结合使用`?`操作符，这些函数还可以将不必要的空检查保持在最低限度。慷慨地使用它们。只是记住不要嵌套太多。事实上，我从不筑巢。它会引入不必要的深度，连同可能的`it` & `this` & `return`头痛。

总而言之，我认为 Kotlin 是一种非常令人满意的语言。我计划更深入地研究这种语言，探索更多的技术来编写更干净、更易管理的代码。我建议你也这样做。下次见！