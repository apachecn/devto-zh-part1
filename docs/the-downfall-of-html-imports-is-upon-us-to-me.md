# HTML 进口的衰落就在我们身上(对我来说)

> 原文：<https://dev.to/nektro/the-downfall-of-html-imports-is-upon-us-to-me>

> [不推荐使用]不推荐使用 HTML 导入中定义的样式表对主控文档进行样式设置，计划于 2018 年 3 月左右在 M65 中删除。

我今天刚刚在我的 Chrome 浏览器更新到 M61 后在我的主机上读到了这篇文章。这是我今天读到的最悲伤的消息。HTML 进口衰落的下一步。我简直不敢相信它会发生，因为它是 CSS/JS 库、框架，当然还有定制元素的完美交付方法。

当我看到这个的时候，我第一次注意到了末日的开始:

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)# 645](https://github.com/w3c/webcomponents/issues/645)

[![justinfagnani avatar](img/d8e6e3c7be01249846f6b00d988a0ccc.png)](https://github.com/justinfagnani) **[justinfagnani](https://github.com/justinfagnani)** posted on [<time datetime="2017-06-20T15:30:13Z">Jun 20, 2017</time>](https://github.com/w3c/webcomponents/issues/645)

既然 JavaScript 模块即将得到广泛的浏览器支持，我们应该考虑一个能很好地支持它的 HTML 模块系统。HTML 导入没有导出符号的机制，也不能由 JavaScript 导入，但是它的加载行为与 JavaScript 模块完全兼容。

@dglazkov 在这里勾画了一个 HTML 模块的提案:[https://github . com/dglazkov/web components/blob/HTML-modules/proposals/HTML-Imports-and-ES-modules . MD](https://github.com/dglazkov/webcomponents/blob/html-modules/proposals/HTML-Imports-and-ES-Modules.md)

要点是，使用 JavaScript 模块管道，您可以导入 HTML。

或者以 HTML 格式:

```
<script type="module" url="foo.html">
```

Enter fullscreen mode Exit fullscreen mode

或者 JavaScript:

```
import * as foo from "foo.html";
```

Enter fullscreen mode Exit fullscreen mode

但是在这个草图的范围内，仍然有几个关于从 HTML 导出符号和导入到 JavaScript 的细节问题。

[View on GitHub](https://github.com/w3c/webcomponents/issues/645)

建议对 HTML 导入进行修改，通过 Javascript 而不是通过`<link rel="import">`添加功能。虽然我并不完全反对能够在 JS 中导入`<template>`元素之类的想法，但我讨厌用*取代 HTML 方式的想法。*

我喜欢自定义元素的想法，老实说，这是我从做网站开发以来看到的最喜欢的功能。我有一个专门存放定制元素的仓库，在那里我制作了一堆。其中最引人注目的部分是一个文件夹，里面有一堆受[流畅设计](http://fluent.microsoft.com/)启发的元素。

而且整个项目可以用一行。

```
<link rel="import" href="https://rawgit.com/Nektro/custom-elements/master/fl/fl.html"> 
```

Enter fullscreen mode Exit fullscreen mode

这个文件设置一些基本的 CSS，并导入所有其他元素。然而，Chrome 是唯一一个拥有[原生支持](https://caniuse.com/#feat=imports)的浏览器。其他所有人都必须使用 bodged polyfill，因为出于某种原因，其他所有浏览器都没有兴趣实现它。

最后，我希望这个基于 HTML 的特性在 HTML 中继续得到支持。