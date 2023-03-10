# 学习 Scala for Spark 及其应用方法

> 原文：<https://dev.to/wrschneider/learning-scala-for-spark-and-the-apply-method-gjf>

*本文最初出现在[我的博客](http://wrschneider.github.io/2017/12/12/scala-spark-apply.html)上*

有时在 Spark 中你会看到类似
的代码

```
val df1 = ...
val df2 = ...
val df3 = df1.join(df2, df1("col") === df2("col")) 
```

Enter fullscreen mode Exit fullscreen mode

一开始像方法一样使用`DataFrame`对象有点奇怪。

这是怎么回事？

在 Scala 中，对象有一个`apply`方法，允许任何对象像方法一样被调用。`obj(foo)`相当于`obj.apply(foo)`。DataFrame 的`apply`方法和`col`一样，所以`df("col")`相当于`df.col("col")`。

这也和为什么可以不用`new`创建 case 类的实例有关——一个 case 类定义了一个同名的 companion 对象，而那个
companion 对象有一个返回`new ClassName()`的`apply`方法。

就我个人而言，我还没有学会喜欢 Scala 的`apply`特性，因为它并不完全清楚`obj(foo)`应该做什么。但是在这种情况下，
当我把 Scala 看作 Spark 的 DSL 时，有这样的快捷方式是有意义的。