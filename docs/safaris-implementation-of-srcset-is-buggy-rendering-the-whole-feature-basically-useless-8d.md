# Safari 对 srcset 的实现漏洞百出，使得整个特性基本上毫无用处

> 原文：<https://dev.to/ben/safaris-implementation-of-srcset-is-buggy-rendering-the-whole-feature-basically-useless-8d>

我很希望有人能指出一个解决方案，但是我发现如果存在`srcset`属性，那么在 Safari 中通过 AJAX 加载的图像根本无法呈现。我无法让它工作，[找到了其他一些有同样问题的人](https://stackoverflow.com/questions/40807500/responsive-images-using-srcset-sizes-isnt-respected-by-safari-ios)。属性允许你根据窗口的大小定义几个不同的源。它有可能成为 web 性能的主要资产。

用 JavaScript 而不是 HTML 来决定图像的选择可能会降低性能，而且对于一个本应简单的特性来说，基于设备头的选择是一个复杂的解决方案。`srcset`属性在工作时是一个很好的特性，但是在 Safari 中远离边缘情况的情况下它会失败。这基本上使整个功能无用或充其量脆弱。

这篇文章认为“不一致的实现”是一个特征而不是一个错误，因为它迫使“浏览器去竞争”。我不知道这是什么意思，或者这个问题是否可以追溯到这种思路。但如果结果是这样，我当然不同意这种做法。

我计划研究一下`<picture>`元素作为可能的替代品。我还不知道它是否有同样的问题。对于大多数典型的用例，通常建议“只使用`srcset`”。但是由于没有正确实施，搬到`<picture>`对我来说是一个很好的解决方案。