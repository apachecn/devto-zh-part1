# 为什么在 Elm 中不需要多案例匹配

> 原文：<https://dev.to/leojpod/why-is-there-no-need-for-multicase-matching-in-elm>

作为 Elm 的初级开发人员，我很快就爱上了 union 类型和 _ 操作符，但是发现缺少多案例匹配有点令人恼火，直到…直到我看到了[灯](http://stackoverflow.com/questions/41118171/handling-multiple-match-cases-with-single-statement-in-elm)！

### TL；博士？

每当您的联合类型的几个构造函数共享某些行为时，请使用以下模式，并远离 _ 运算符！