# 需要 OAuth/OAuth2/OpenID 连接吗？

> 原文：<https://dev.to/grahamcox82/do-you-need-oauthoauth2openid-connect>

TL；DL 没有，但是你也可以用一些。

几乎所有的单页面应用程序都需要某种与服务器通信的方式来获取它们想要处理的数据。他们中的许多人希望拥有个人数据，这意味着知道用户是谁。传统的方法是使用会话状态，但是这种方法伸缩性不好。最近更加强调访问令牌——本质上是存储在客户机上的会话状态。这种方法的伸缩性更好，但是您需要某种方法来获得令牌以供使用。

这通常是 OAuth2 或 OpenID Connect 之类的东西出现的地方。这些提供了一些框架，在这些框架中，客户端可以获得一个访问令牌，用于与服务器对话。并且它们很难正确实现。

我的个人建议是暂时忽略 OAuth2 的大部分。在您的服务器中实现访问令牌 auth，并实现 OAuth2 隐式流的简化版本，并保留它。

我说的简化隐式流程是什么意思？您只需打开一个页面供用户登录，该页面重定向到一个硬编码的 URL，其中包含访问令牌的详细信息。然后，这个硬编码的页面可以访问其 windows 父页面中的 JavaScript 函数，并将访问令牌传递给该函数。任务完成。不需要担心客户端认证、范围、重定向 URIs、刷新令牌、多流支持或任何其他需要的东西。

这样做，你也处于一个位置，如果你需要，你可以在未来实现 OAuth2，它只是下降。或者你可以使用第三方认证——例如谷歌或推特——这就可以了。实现非常简单，如果将来需要重新实现，只需做很少的更改。

记住——YAGNI 和 MVP 才是王道。不到需要的时候不要做事，因为你可能永远都不需要。