# Java 8 功能接口清单

> 原文：<https://dev.to/monknomo/java-8-functional-interface-cheatsheet>

O'Reilly 有一篇关于 Java 8 中函数接口的非常深入的文章。这里有一个快速的备忘单来记录主要的类型、它们的作用和常见的用例。

## `Predicate`

A `Predicate`返回真或假。这些用于过滤器或替换 if/else 逻辑的大链。

## `Function`

一个`Function`转换数据。这些用于地图和其他变换。重要的是不要改变传入的原始数据。

## `Supplier`

`Supplier`不接受任何参数，返回一个已知类型的值。获取、读取或创建供其他功能使用的资源是常见的用例。供应商让事情开始。

## `Consumer`

A `Consumer`接受单个参数，但不返回任何结果。消费者是变异功能，有副作用的东西应该去的地方。消费者完成事情。

如果你喜欢这个，请访问我的博客。

[每月收到一封电子邮件，其中包含来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)

*感谢 [Rog01](https://flic.kr/p/cYeye5) 给了我一张很棒的机器人照片*