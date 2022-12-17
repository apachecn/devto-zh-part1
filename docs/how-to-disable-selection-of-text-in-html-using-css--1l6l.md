# 如何使用 CSS 禁用 html 中的文本选择？

> 原文：<https://dev.to/moreonfew/how-to-disable-selection-of-text-in-html-using-css--1l6l>

根据您的需求，您可能需要使用 CSS 或 JavaScript 在 HTML 中禁用文本选择或文本高亮显示。虽然有不同的方法可以做到这一点，但您可以使用 CSS 轻松地禁用文本突出显示。考虑这样一种情况，你有一个复制粘贴功能，如优惠券代码等，你想让用户选择粘贴在某个地方，但同时你不希望用户选择一些其他文本，如标签文本等。在这种情况下，除了你希望用户选择的特定文本，比如优惠券代码等，你必须禁止选择其他文本。您可以使用 CSS 属性`user-select`轻松地使文本不可选择。

## 禁用选择是 CSS 使用用户选择属性

您可以使用以下 CSS 代码禁用选择:

```
.no-select {
-webkit-user-select: none; /* Chrome all / Safari all */
-webkit-touch-callout: none; /* iOS Safari */
-khtml-user-select: none; /* Konqueror */
-moz-user-select: none; /* Firefox all */
-ms-user-select: none; /* IE 10+ */
user-select: none; /* Common (limited support) */
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在我写这篇文章的时候，浏览器对`user-select`的支持非常有限。但是，您可以在[的 canuse](http://caniuse.com/#search=user-select)查看最新的支持列表。总之，我想说你可以使用`user-select` css 属性来禁用选择是 css，但是，请检查浏览器的支持，然后再继续在任何生产网站上使用它。

如何使用 CSS 禁用 html 中的文本选择？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。