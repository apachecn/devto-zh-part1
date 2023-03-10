# 使用 axios 和 aws4 调用 Amazon API 网关认证方法

> 原文：<https://dev.to/joshuaakahn/calling-amazon-api-gateway-authenticated-methods-with-axios-and-aws4-1hi8>

[亚马逊 API 网关](https://aws.amazon.com/api-gateway/)提供了一个方便易用的服务，允许开发者发布、监控和维护 API。它还提供了自定义业务逻辑和常见需求(如缓存、节流和授权)之间的关注点分离。

在最近的一个项目中，我需要保护我的 API，这样只有授权用户才能调用它们(例如管理员端点)。API Gateway 支持多种方法来控制对服务的访问。我还需要为一群用户提供身份验证，并选择利用 AWS 强大的 IAM 功能来控制通过 [Amazon Cognito](https://aws.amazon.com/cognito/) 的访问。Cognito 提供用户管理和联合身份，以提供对 AWS 资源的安全访问，包括[调用 API 网关方法](https://docs.aws.amazon.com/apigateway/latest/developerguide/permissions.html)。

足够的背景，对代码…

在前端，我使用了流行的 [axios](https://github.com/mzabriskie/axios) HTTP 库和 [aws4](https://github.com/mhart/aws4) ，一个使用 [AWS 签名 v4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) 来签署请求的库。虽然 API 网关的配置超出了本文的范围，但是我们需要签名并提供一个`Authentication`头，以便安全 API 允许调用。这就是 aws4 帮助实现的。签署请求允许前端承担被授权调用 API 的 AWS 角色。

注意:下面的代码片段假设用户已经通过 Cognito 进行了身份验证，并检索了临时凭证(包括访问密钥、秘密密钥和会话令牌)。

首先，下面的代码演示了一个受`AWS_IAM`授权保护的 API 的`GET`:

```
400: Invalid request

```

接下来，让我们考虑一下对于一个`PUT`请求，上述内容是如何变化的。请注意请求主体和内容类型头的添加。

```
400: Invalid request

```

我希望当你使用这些伟大的前端包和 Amazon API Gateway 时，上面的内容对你有用。