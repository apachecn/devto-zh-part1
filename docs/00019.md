# 如何临时存储 API 响应？

> 原文：<https://dev.to/chandlerbing016/how-to-temporarily-store-api-response-ied>

我目前正在开发一个基于 Laravel 的应用程序，让用户访问他们的 Google drive，并在我们的前端列出他们所有的文件。

我的问题是，我如何临时存储这个 API 数据，以便即使用户刷新页面，她/他也不必重新重复 google 同意屏幕步骤，并且数据可以显示给用户？

我读了一些关于这方面的帖子，尽管我对可以用来实现这一点的不同技术感到困惑。像`Redis`、`Varnish`、`HTML5 storage`这样的技术人员，还有最重要的`Nginx caching gateway`。

你能解释一下吗？谢谢