# 假人用 OAuth

> 原文：<https://dev.to/rmorschel/oauth-for-dummies>

如果你曾经使用你的脸书或谷歌证书访问过一个网站，那么你就遇到了 OAuth。OAuth 是一种授权网络协议，用于授予网站访问您的一些个人信息或访问某些系统的权限。通常给出的类比是代客钥匙，这对我来说毫无意义，因为我有一辆生锈的大众甲壳虫，但我们不要担心这个。

如果你要建立这样一个网站，你需要向 OAuth 提供商(Google 或其他什么)注册。他们会给你一个应用程序 id 和密码。这个秘密允许你对提供者说 OAuth。

所以，很久以后，乔·布洛格斯出现了，他想访问你的网站。您的网站向 OAuth 提供者发送一个请求(带有您的应用程序 id 和密码),并获得一个对话令牌。

然后，您将用户重定向到 OAuth 提供者(但是包括对话令牌和回调 URL，这将在您注册应用程序时包括在内)。OAuth 检查对话令牌和 URL，然后确定用户是谁(例如，要求您登录)，然后显示一个同意屏幕:“您想允许应用程序 X 访问您的 Y 吗？”你检查一下 Y 列表，然后说好(或者不好)。然后，OAuth 提供者通过浏览器向您的网站发回一个 HTTP post，带有两个令牌:

*   一个访问令牌(代客钥匙)，它允许您向 OAuth 提供者请求任何东西
*   刷新令牌(用于当访问令牌到期时，这将是频繁的)

等一下！这些 Y 的东西呢？OAuth 提供者如何知道网站想要什么权利？

OAuth 称之为“范围”。作用域是允许您访问的用户“事物”的名称。它可以是用户名、电子邮件或一组访问权限。当网站进行 OAuth 时，它会在 OAuth 请求中添加一些范围，如果它们在允许列表中，就会呈现给用户。

基本上就是这样。还有一些其他的细微差别和东西，但我的车又坏了，我需要运行。