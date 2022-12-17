# 我们可以发布不是铁板一块的移动应用吗？

> 原文：<https://dev.to/willluce/can-we-ship-mobile-apps-that-are-not-monolithic>

在 web 应用程序中，将几个小型应用程序通过域路由组合起来，作为一个服务提供给用户是很常见的。我可以使用单独的应用程序来登录、管理个人资料和聊天:

*   [www.website.com/login](http://www.website.com/login)
*   [www.website.com/profile](http://www.website.com/profile)
*   [www.website.com/chat](http://www.website.com/chat)

这是一个微不足道的例子，但它服务于它的目的。对用户来说，它是一个网站，但对开发者来说，它是几个小应用程序。这带来了很多好处，主要归结为拥有一个小的代码库。

我正在开始移动开发(react-native ),想知道这种模式是否已经进入了那个环境？由于可交付物的类型，我的直觉是“不”。一个移动应用程序作为一个东西被交付到一个应用程序商店，在那里一个网站可以通过 URL 映射来“组成”，但我也知道开发者是聪明的，我可能完全错了。