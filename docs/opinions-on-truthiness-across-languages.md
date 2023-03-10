# 关于跨语言真实性的看法

> 原文：<https://dev.to/wrschneider/opinions-on-truthiness-across-languages>

*本文的一个版本最初出现在[我的 GitHub pages 博客](http://wrschneider.github.io)T3 上*

当在一个`if`语句中使用一个非布尔对象作为表达式时，不同的语言对于什么被视为“真”或“假”有不同的看法。

我看了 Python、Groovy、Javascript 和 Ruby，比较它们的区别。

*   空总是假的
*   零字符串和空字符串是假的，除了在 Ruby 中
*   空集合(set/list/dict)在 Python 和 Groovy 中是错误的，但在 Javascript 或 Ruby 中不是

我对语言设计的观察和个人看法:

*   Python 将零、空字符串和集合都视为‘falsy’。我个人觉得这是最直观的约定。
    *   从 c 语言开始，将零和空作为假来处理就有了历史先例。假指针和空指针在寄存器或内存位置中都表示为零。
    *   考虑到我多次编写类似于`if (foo != null and !foo.empty())`的条件，处理空字符串和集合是一个很好的便利。这通常是个例外，我想在一个条件中区分 null 和 empty。所以很好的是，`if (foo)`处理常见的情况，然后当我真的想区分 null 时，我可以写`if (not foo is None)`。
    *   从我的 Oracle 经验来看，将空字符串视为类似于 null 感觉很熟悉。此外，这与空集合的处理是一致的。
*   Groovy 受到 Python 的启发，并采用类似的惯例来保证真实性。
*   Ruby 持不同意见，认为除了`nil`(当然还有`false`)之外，所有的值都是真实的。虽然这不是我个人的偏好，但这是可以辩护的，也是前后一致的。
*   Javascript 可以可靠地交付 WTF。Javascript 将零和空字符串视为虚假，而将空集合视为真实。对我来说，很难理解为什么字符串和集合应该有不同的行为；Python 行为更有意义。但是等等，还有更好的:看看 StackOverflow 上的这个[链接。](http://stackoverflow.com/questions/5491605/empty-arrays-seem-to-equal-true-and-false-at-the-same-time)