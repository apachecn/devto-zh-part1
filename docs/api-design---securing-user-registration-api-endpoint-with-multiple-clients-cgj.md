# API 设计——保护多个客户端的用户注册 API 端点

> 原文：<https://dev.to/luke_redroot/api-design---securing-user-registration-api-endpoint-with-multiple-clients-cgj>

我目前正在开发一个概念验证系统，它包含一个后端 API，可以被多个客户端应用程序使用，包括移动客户端和一个同构的 React 客户端。

我已经在很大程度上处理了身份验证和授权，唯一剩下的问题(至少我能看到)是注册用户的路径——POST/users/，因为它是公共的，不需要授权。除了实现 CORS 和速率限制之外，我主要关心的是保护路由免受恶意垃圾邮件的侵害。

例如`curl -X POST -d "{ email: 'hello@gmail.com', password: 'nahnah' }" http://host.com/api/v1/users`

我在这里有什么选择？

我想到的一个解决方案是，强制 SPA 将注册表单提交到服务 SPA 的服务器内的快速路由(它需要 SSR)以在服务器上进行 API 调用，这意味着浏览器的网络选项卡将没有 API 调用的记录。这意味着我还必须在 SPA 中实现一个 CSRF 令牌。还是用 reCaptcha？

还有，这是不是一个坏主意？