# 引发或不引发异常，以及设计返回值的艺术

> 原文：<https://dev.to/mottalrd/to-raise-or-not-to-raise-exceptions-and-the-art-of-designing-return-values-3m7>

每当我们调用一个函数来执行一些可能成功或失败的操作时，我们总是面临同样的困境。返回值应该是什么？如果失败了，我应该返回零吗？或者我应该抛出一个异常？失败到底意味着什么？

像每个有趣的问题一样，答案是视情况而定。在这篇博文中，我将使讨论更加清晰，并展示其中涉及的权衡以及如何在现有方案中做出选择。

[在这里阅读完整的故事](http://www.alfredo.motta.name/to-raise-or-not-to-raise-exceptions/)