# [Devtools]编辑页面上的任何文本

> 原文：<https://dev.to/duchienvuong/devtools-edit-any-text-on-page>

当改变你的文本时，想在你的页面上测试用户界面吗？想要直接在浏览器中编辑一些文本吗？

现在有了`designMode`,你可以将浏览器用作文本编辑器。

转到控制台并键入:

`document.designMode = 'on'`

打开它。有效值为`'on'`和`'off'`，在大多数浏览器中默认为`'off'`。

你现在可以编辑 DOM 中的任何东西。

调试愉快！

*最初发布在我的博客上[这里](https://hienvd.com/2017/02/17/DevTools-Edit-any-text-on-page/)T3】*