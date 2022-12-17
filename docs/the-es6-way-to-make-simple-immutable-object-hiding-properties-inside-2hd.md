# ES6 的方式使简单的不可变对象在内部隐藏属性

> 原文：<https://dev.to/shqld/the-es6-way-to-make-simple-immutable-object-hiding-properties-inside-2hd>

只要我们要享受 OOP 的好处，保持 FP 的对象不变以及将一些属性设为私有基本上是好的。为了使不可变的 dict 封装属性，现在我们有两个选项:Symbol 和 WeakMap，这是作为 ES2015 的新功能引入的。

因此，我实验性地制作了一些模型，但没有考虑性能。很高兴听到你的评论！