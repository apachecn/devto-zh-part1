# Zip Haskell 介绍 Zip 函数

> 原文：<https://dev.to/jreina/intro-to-the-zip-function>

*本文最初发表于[我的 Github 页面网站](http://johnnyreina.com/programming/functional/2017/09/19/intro-zip.html)2017 年 9 月 19 日*

既然我们已经讨论了基本的迭代函数，我想花点时间看看更多的函数，这些函数通常隐藏在函数优先编程语言或函数实用程序库中。虽然不像 map、filter 和 fold 那样普遍，但这些函数有其使用案例，尽管它们可以通过 fold 的方式轻松实现，但它们更适合特定用途，因此您会发现语法更加整洁。

我想说的第一个函数是卷积函数，更广泛的说法是 zip。在 Haskell 中，zip 函数有一个类型签名`zip :: [a] -> [b] -> [(a, b)]`。这意味着 zip 函数接受两个列表，并通过合并每个列表的每个值将它们合并成一个列表。传统上，zip 输出一个元组(类型签名的`(a, b)`部分)，然而，由于 JavaScript 没有元组(也没有 zip 函数)，我们将使用一个双值数组来表示对，并且我们将自己定义 zip 函数。

我们首先将 zip 定义为 map 的一个函数。