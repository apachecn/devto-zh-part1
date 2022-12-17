# 在 Python 中管理类属性

> 原文：<https://dev.to/adekoder/managing-class-attributes-in-python-2g8a>

[![](img/fe65a1064e96fe9cf05c96ffb13f38c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dg71qM6k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AORR77SMEwnjm95bJmZSbWg.png)

大家好，在这篇博文中，我们将深入探讨一些 python 编程技巧，或者我应该说我们作为 python 开发者可以利用的特性，我们都知道 python 中的每一个东西都是对象，类是对象，实例是对象，函数是对象等等。

我们都知道任何时候我们这样做:

```
object.attribute or object.attribute = value` 
```

Enter fullscreen mode Exit fullscreen mode

我们试图获取或设置一个对象的属性，这在所有 python 程序中都很常见，尤其是在使用**类时。**

#### 那又怎样？你为什么要在乎？

那么，您为什么要担心属性管理呢？现在让我来分解一下，这里有一些可能的场景:

#### 用例 1

我们知道在 python 中，我们可以通过用 obj.attribute.call 引用任何类属性来访问它，这意味着我们的属性对各种操作是开放的，比如读、写、删除。下面是代码片段中的一个示例: