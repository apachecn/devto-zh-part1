# 6 个常见的 API 错误

> 原文：<https://dev.to/runscope/6-common-api-errors>

你有没有使用过返回 HTML 错误页面而不是你期望的 JSON 的 API，导致你的代码爆炸？如果收到一个 200 OK 状态代码，而您的响应中有一条神秘的错误消息，该怎么办？

构建一个 API 可以像提供快餐一样快。像 Express、Flask 和 Sinatra 这样的框架结合 Heroku 或 [zeit 的 now](https://zeit.co/now) 可以帮助任何开发人员在几分钟内建立并运行一个 API。

然而，构建一个真正安全、健壮、丰富的 API 可能需要更多的工作，就像厨师做一顿大餐需要更多的时间一样。你需要优秀的文档、清晰简洁的错误信息，并满足开发者对你的 API 应该如何工作的期望。

在桌子的另一边，我们有与这些 API 交互的开发人员。而我们作为开发者，有时候也会犯错误。我们可能对端点应该如何工作做出错误的假设，没有足够仔细地阅读文档，或者那天早上没有足够的咖啡来解析错误消息。这就是 Runscope 的用武之地。

我们的[测试和监控工具](https://www.runscope.com/api-monitoring)可以帮助您发现那些因缺乏集成测试或真实用例场景而隐藏的问题。与成千上万的开发人员一起解决他们的 API 问题，让我们对他们在与 API 集成和交互时经常看到的问题有了独特的见解。

以下是我们列出的 6 个可能让你措手不及的常见错误，它们发生的原因，以及你如何避免它们:

## 1。使用 http://而不是 https://

在测试 API 时，忘记一个“s”会给你带来很多麻烦。有些 API 可能只支持 HTTPS，而有些 API 可能只支持某些端点的 HTTP，而不支持其他端点。

即使 API 同时支持这两者，您仍然可能会遇到一些错误。例如，一些 API 将 HTTP 流量重定向到它们的 HTTPS 对应方，但是并不是所有的框架都将被配置为遵循 302 状态代码。 [Node.js `request`模块](https://github.com/request/request#requestoptions-callback)举例来说，默认情况下会遵循 GET 重定向，但是如果想要遵循 POST 和其他方法的重定向，就必须显式地将`followAllRedirects`设置为`true`。

API 也可能会停止支持 HTTP，所以保持最新的变化是很重要的。好的 API 提供商会通过电子邮件和他们拥有的任何社交媒体渠道让用户提前知道。你可以采取的另一个步骤是使用像 [Hitch](https://www.hitchhq.com/) 这样的工具，它可以让你遵循特定的 API，如果有任何变化，它会通知你。

如果你问自己你的 API 是否应该支持 HTTPS，那么答案是肯定的。获得证书的过程曾经很麻烦，但有了像 [Let's Encrypt](https://letsencrypt.org/) 和 [Cloudflare](https://www.cloudflare.com/) 这样的解决方案，没有理由不支持 HTTPS。如果你不确定为什么你应该这样做，或者认为你不应该这样做，因为你没有传输任何敏感数据，我强烈推荐阅读“[为什么 HTTPS 的一切？](https://https.cio.gov/everything/)“来自 CIO.gov。

## 2。意外错误代码

一个好的 API 错误消息将允许开发人员快速找到为什么以及如何修复一个失败的调用。一个坏的 API 错误消息将导致血压升高，以及大量的支持票和浪费时间。

几周前，我在试图检索 API 的访问令牌时遇到了这个问题。代码授权流将返回一条错误消息，说我的请求无效，但它不会给我任何更多的细节。在一个小时的碰壁之后，我意识到我没有注意文档，忘记了包含一个 Authorization header，它是我的应用程序的 client_id 和 client_secret 的 base64 编码字符串。

良好地使用 HTTP 状态代码和清晰的错误消息可能并不性感，但这可能是开发人员宣扬您的 API 和愤怒的 tweet 之间的区别。

Steve Marx 在“[中说过，你的 API 应该使用多少 HTTP 状态代码？](https://blogs.dropbox.com/developers/2015/04/how-many-http-status-codes-should-your-api-use/)":"...如果一个 API 遵循与他们熟悉的其他 API 相同的约定，开发人员将更容易学习和理解它。”作为 API 提供者，您不必实现 70 多种不同的状态代码。史蒂夫的另一个好建议是:

> 按照这种务实的方法，API 应该至少使用 3 个状态代码(例如 200、400、500 ),并且应该增加在多个 API 中具有特定的、可操作的含义的状态代码。除此之外，记住你的特定开发者受众，并努力满足他们的期望。”

Twilio 是关于[状态代码和错误消息](https://www.twilio.com/docs/api/errors#debugging-calls-to-the-rest-api)的最佳实践的一个很好的例子。他们做了额外的工作，并在他们的响应中包含链接，所以错误消息是简洁的，同时仍然为开发人员提供更多的信息，以防他们需要。

```
{  "code":  21211,  "message":  "The 'To' number 5551234567 is not a valid phone number.",  "more_info":  "https://www.twilio.com/docs/errors/21211",  "status":  400  } 
```

Enter fullscreen mode Exit fullscreen mode

作为 API 消费者，我们需要小心，不要认为 API 200 状态代码意味着请求成功调用并返回了我们想要的信息。有些 API，比如脸书的 Graph API，总是返回 200 状态码，错误包含在响应数据中。因此，在测试和监控 API 时，一定要小心，不要自动假设 200 表示一切正常。

关于响应处理的另一个很好的资源是 Mike Stowe 关于“ [API 最佳实践:响应处理](http://blogs.mulesoft.com/dev/api-dev/api-best-practices-response-handling/)”的博客文章

## 3。使用不正确的方法

这是一个简单的问题，但是出奇的普遍。很多时候这可以归咎于糟糕的文档。也许端点没有明确说明 GET/POST/PUT 等之间支持什么方法。或者他们用错了动词。

如果你不小心，工具也会捉弄你。例如，假设您想要使用请求体发出 GET 请求(这不是一个很好的实践，但确实会发生)。如果您使用-d 选项发出 curl 请求，并且不使用`-XGET`标志，它将自动默认发布并包含`Content-Type: application/x-www-form-urlencoded`头。

Daniel Stenberg(curl 的作者和维护者)关于"[curl-X](https://daniel.haxx.se/blog/2015/09/11/unnecessary-use-of-curl-x/)的不必要使用"的这篇文章也说明了在处理重定向时可能会遇到的另一种可能性:

> 最明显的问题之一是，如果您还告诉 curl 遵循 HTTP 重定向(使用-L 或 location)，-X 选项也将用于重定向到的请求，这可能根本不是服务器要求的和用户期望的

其他时候，我们可能会陷入过去的假设，只是使用了错误的方法。例如，Runscope API 在创建新资源(比如测试步骤或环境)时使用 POST，在修改它们时使用 PUT。但是 Stripe 的 API 在创建和[更新对象](https://stripe.com/docs/api#update_customer)时使用 POST 方法。

这两种方法都是有效的，Stormpath 有一篇很棒的博客文章谈到了它们的区别，以及作为 API 提供者如何处理它们。无论您选择哪一个，只要在整个 API 中保持一致，并确保拥有正确和最新的文档，这样您的用户就不会遇到这个错误。

## 4。发送无效的授权凭据

实现 OAuth 2 的 API，比如 [PayPal](https://developer.paypal.com/docs/api/auth-headers/) ，通常要求开发者为每个请求包含一个`Authorization`头。很容易将它与`Authentication`混淆(我在为我们上一篇博文制作 gif 时就是这么做的。)，所以如果你的请求失败了，确保你用了正确的词。

授权头的另一个问题是如何正确地构造它。OAuth 2 令牌需要在前面加上“载体”才能工作:

`Authorization: Bearer your_api_token`

使用 HTTP 基本身份验证时，密切注意头值的语法也很重要。其形式如下:

`Authorization: Basic base64_encode(username:password)`

常见的错误包括忘记了“基本”(注意空格)前缀，没有对用户名和密码进行编码，或者忘记了它们之间的冒号。如果一个 API 提供者只需要一个没有密码的用户名(比如 Stripe，其中 API 键就是用户名)，那么即使没有密码，用户名后面也需要一个讨厌的冒号。

## 5。未指定内容类型或接受标头

Accept 和 Content-Type 头协商将在客户端和服务器之间发送或接收的信息的类型。一些 API 将接受不包含任何这些头的请求，只是默认为 JSON 或 XML 之类的通用格式。

其他 API 稍微严格一点。如果您不明确 Accept 头值，有些可能会返回 403 错误，并要求您在请求中包含这些头。这样，服务器知道客户机发送的是什么信息，也知道它们期望收到什么格式的返回。

如果您用不同工具测试您的 API，这个问题也可能导致一些混乱。例如，curl 和其他流行的测试工具会自动为每个请求包含一个任何 MIME 类型的`Accept`头:`*/*`。在 Runscope，我们不添加默认的 Accept 头，所以当测试同一个端点时，这会得到不同的结果。

## 6。出现错误时，API 返回无效的内容类型

[![An API response with a full HTML page in the body parameter](img/5666a67ff3c420b73f67a115e7b4e53f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XrCV4_tm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/51814c87e4b0c1fda9c1fc50/t/588f6f433e00be6360b38288/1485964419721/html-response-3.jpg)

我可以说这是我最讨厌 API 的地方之一。看到回复中的那一行让我血压飙升。

嗯，有时候那是我的错。如果您忘记在请求中发送一个`Accept`头，API 就不能确定您期望的响应格式。

对于 API 提供者，一些框架和 web 服务器默认为 HTML。比如 PHP 框架 [Symfony](https://symfony.com/) ，默认返回 500 HTML 错误。因此，如果您正在创建一个没有返回 HTML 的业务的 API，请确保检查默认的错误响应。

发生这种情况的另一个原因可能与您的 API 无关，而是与位于您的 API 前面的路由网格或负载平衡器有关。例如，如果您的 API 前面有一个 nginx 实例，当它遇到请求超时或其他错误时，它可能会在您的 API 实例有机会知道发生了什么之前返回一个 HTML 错误。

## 结论

这些是我们在多个 API 中看到的一些最常见的错误。这个列表还可以持续更长时间，所以如果你遇到了其他错误并设法修复了它，请与我们分享。

作为 API 提供者和 API 消费者，这些错误有时会被忽视，浪费数千个调试小时。为了更好地了解您的 API，避免陷入不良重定向或意外错误代码，请使用[免费试用帐户](https://www.runscope.com/signup)查看我们的 API 测试和监控工具。