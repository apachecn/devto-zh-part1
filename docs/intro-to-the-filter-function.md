# 过滤功能介绍

> 原文：<https://dev.to/jreina/intro-to-the-filter-function>

*本文最初发表于[我的 Github 页面站点](http://johnnyreina.com/programming/functional/2017/09/15/intro-filter.html)2017 年 9 月 15 日*

这听起来可能有点古怪或可笑，但我现在很少写循环。我发现，几乎每种编程语言都包含一组方法或适用的函数，可以代替我以前编写的每一个循环。这些[高阶函数](https://github.com/hemanth/functional-programming-jargon#higher-order-functions-hof)被称为映射、过滤和折叠。

## 滤镜

`filter`函数接受一个谓词，该函数从数组中接受一个项并返回一个布尔结果，并返回一个新数组，该数组包含当通过谓词时返回 true 的元素。

### 婴儿步伐

我们将从一些简单的例子开始: