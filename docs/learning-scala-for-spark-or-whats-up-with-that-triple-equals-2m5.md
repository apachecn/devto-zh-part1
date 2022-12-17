# 为 Spark 学习 Scala，或者，三重等于是怎么回事？

> 原文：<https://dev.to/wrschneider/learning-scala-for-spark-or-whats-up-with-that-triple-equals-2m5>

*本文最初出现在[我的博客](http://wrschneider.github.io/2017/09/24/spark-triple-equals.html)上*

我开始专门学习 Scala 来使用 Spark。Scala 中语言特性的数量之多令人应接不暇，因此，我发现在特定用例的上下文中逐个学习 Scala 特性是很有用的。在某种意义上，我把 Scala 当作编写 Spark jobs 的 DSL。

让我们挑出一段简单的 Spark-Scala 代码:`dataFrame.filter($"age" === 21)`。

这里发生了一些事情:

*   `$"age"`创建一个 Spark `Column`对象，引用 dataframe 中名为`age`的列。在隐式类 [`StringToColumn`](https://spark.apache.org/docs/2.1.1/api/java/index.html?org/apache/spark/sql/SQLImplicits.StringToColumn.html) 中定义了`$`操作符。隐式类是与 C#扩展方法或其他动态语言中的混合类似的概念。`$`操作符就像是添加到`StringContext`类中的一个方法。

*   三元等号运算符`===`通常是 Scala 类型安全的等号运算符，类似于 Javascript 中的运算符。Spark 用`Column`中的一个方法覆盖了这一点，创建了一个新的`Column`对象，将左边的`Column`与右边的对象进行比较，返回一个布尔值。因为[双等(`==`)不能被](https://stackoverflow.com/questions/7681183/how-can-i-define-a-custom-equality-operation-that-will-be-used-by-immutable-set)覆盖，Spark *必须*使用三等。

*   `dataFrame.filter`方法接受一个参数`Column`，它定义了应用于`DataFrame`中的行的比较。只有符合条件的行将包含在结果`DataFrame`中。

请注意，当上面的代码行执行时，并不执行实际的比较！像`filter`和`select`这样的 Spark 方法——包括传入的`Column`对象——是懒惰的。您可以将数据帧想象成一个查询构建器模式，其中每个调用都为 Spark 在调用`show`或`write`这样的调用时将要做的事情构建一个计划。这在概念上类似于 LINQ 的`IQueryable`，其中`foo.Where(row => row.Age == 21)`构建了一个计划和一个表达式树，当必须获取行时，例如当调用`ToList()`时，该树将被翻译成 SQL。