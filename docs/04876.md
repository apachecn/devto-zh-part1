# 地图功能介绍

> 原文：<https://dev.to/jreina/intro-to-the-map-function>

*本文最初发表于[我的 Github 页面站点](http://johnnyreina.com/programming/functional/2017/09/13/intro-map.html)2017 年 9 月 13 日*

这听起来可能有点古怪或可笑，但我现在很少写循环。我发现，几乎每种编程语言都包含一组方法或适用的函数，可以代替我以前编写的每一个循环。这些[高阶函数](https://github.com/hemanth/functional-programming-jargon#higher-order-functions-hof)被称为映射、过滤和折叠。

## 地图

`map`函数接受您提供的函数并遍历列表中的每一项，应用您提供的函数并将结果放在新数组的相同位置。因为`map`接受一个数组并返回一个数组，所以您可以将您的`map`调用链接在一起，并以增量方式转换您的数据*。*

### 婴儿步伐

我们将从一些简单的例子开始: