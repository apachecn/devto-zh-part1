# 无服务器授权器-自定义 REST 授权器

> 原文：<https://dev.to/piczmar_0/serverless-authorizers---custom-rest-authorizer-16>

在这一系列文章中，我将解释无服务器框架中无服务器授权器的基础:它们可以在哪里使用，以及如何为 Amazon API Gateway 编写定制授权器。
我说的是“授权者”,但首先是身份验证机制。授权是第二部分。

在我们深入细节之前，让我们想一想有哪些认证技术可用。

*   基础

最简单也是最常见的是基本认证，其中每个请求都在请求头中包含编码的用户名和密码，例如:

```
GET /spec.html HTTP/1.1
Host: www.example.org
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ== 
```

*   HTTP 头中的令牌

这种认证的一个例子是 OAuth 2。和 [JWT](https://jwt.io) 。API 客户端需要首先使用有效负载中的用户名和密码调用登录端点(不安全的)来获取令牌。这个令牌稍后在后续安全 API 调用的头中传递。
一个好的做法是在一段时间后使令牌过期，并让 API 客户端刷新它或再次登录以接收新令牌。

```
GET /resource/1 HTTP/1.1
Host: example.com
Authorization: Bearer mF_9.B5f-4.1JqM 
```

*   使用附加签名参数的查询认证。

在这种身份验证中，签名字符串由普通 API 调用生成，并添加到 URL 参数中。
例如，亚马逊在 [AWS 签名版本 4](http://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html) 中使用了这种认证

上述技术可能还有更多的变体，但是您可以大致了解一下。

何时使用哪种身份验证机制？

答案还是老样子——看情况！

这取决于我们的应用程序是公共 REST API 还是不会暴露在公司虚拟专用网背后的内部服务。有时，这也是安全性和易用性之间的平衡。

让我们以亚马逊 Signature 4 签名请求为例。如果不使用 helpers API 来签署请求，就很难手工创建它们(忘掉 Curl 吧，你可以很容易地将它与 Basic 和 Token 头一起使用)。
另一方面，亚马逊解释说，这些请求是针对重放攻击的(见更多[这里](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html#why-requests-are-signed))。

如果您正在为银行业务构建一个 API，那么它必须非常安全，但是对于大多数非关键任务的情况，令牌头应该没问题。

所以我们选择了认证和授权机制。现在，我们如何用 AWS 实现它呢？

我们可以自己做用户身份存储，也可以使用现有的，就是 Amazon IAM ( [身份和访问管理](https://aws.amazon.com/iam/?sc_channel=PS&sc_campaign=acquisition_PL&sc_publisher=google&sc_medium=iam_b&sc_content=amazon_iam_e&sc_detail=amazon%20iam&sc_category=iam&sc_segment=164224034079&sc_matchtype=e&sc_country=PL&s_kwcid=AL!4422!3!164224034079!e!!g!!amazon%20iam&ef_id=VgfxNgAAAPg@lfIu:20170910060310:s))。

最后一个有这个优势，我们不需要担心用户名和密码在数据库中的安全存储，而是依靠 Amazon。

# 自定义休息授权人

让我们首先来看一个用定制授权器授权的 REST API 的简单例子

### 创建新的 SLS 项目

```
serverless create --template aws-nodejs --path serverless-authorizers 
```

### 添加简单端点/hello/rest

这里的代码是(注意提交 ID)。
端点完全不安全。

### 部署应用程序

```
sls deploy -v function -f helloRest 
```

部署时，它将打印端点 URL，例如:

```
endpoints:
  GET - https://28p4ur5tx8.execute-api.us-east-1.amazonaws.com/dev/hello/rest 
```

### 从客户端调用端点

用 curl 我们可以这样叫它:

```
curl https://28p4ur5tx8.execute-api.us-east-1.amazonaws.com/dev/hello/rest 
```

### 使用自定义授权器的安全端点。

为了简单起见，我们将只比较令牌和 authorizer 函数中的硬编码值。在实际情况下，应在数据库中搜索该值。应该有另一个不安全的端点允许获取请求中发送的用户名和密码的令牌值。

我们的授权人将在 serverless.yml 中定义如下:

```
functions:
  authorizerUser:
    handler: authorizer.user
  helloRest:
    handler: helloRest.handler
    events:
      - http:
          path: hello/rest
          method: get
          authorizer: ${self:custom.authorizer.users}

custom:
  stage: ${opt:stage, self:provider.stage}
  authorizer:
    users:
      name: authorizerUser
      type: TOKEN
      identitySource: method.request.header.Authorization
      identityValidationExpression: Bearer (.*) 
```

在 http events 部分，我们将授权者定义为:

```
authorizer: ${self:custom.authorizer.users} 
```

这将链接到我们用名称`authorizerUser`定义授权者的自定义部分。这实际上是一个函数的名字，我们在`functions`部分定义为:

```
functions:
  authorizerUser:
    handler: authorizer.user 
```

`handler`指向一个文件，其中授权者处理函数由命名约定定义:`authorizer.user`表示文件`authoriser.js`带有导出的`user`函数。

实现将如下所示:

```
'use strict';

const generatePolicy = function(principalId, effect, resource) {
  const authResponse = {};
  authResponse.principalId = principalId;
  if (effect && resource) {
    const policyDocument = {};
    policyDocument.Version = '2012-10-17';
    policyDocument.Statement = [];
    const statementOne = {};
    statementOne.Action = 'execute-api:Invoke';
    statementOne.Effect = effect;
    statementOne.Resource = resource;
    policyDocument.Statement[0] = statementOne;
    authResponse.policyDocument = policyDocument;
  }
  return authResponse;
};

module.exports.user = (event, context, callback) => {

  // Get Token
  if (typeof event.authorizationToken === 'undefined') {
    if (process.env.DEBUG === 'true') {
      console.log('AUTH: No token');
    }
    callback('Unauthorized');
  }

  const split = event.authorizationToken.split('Bearer');
  if (split.length !== 2) {
    if (process.env.DEBUG === 'true') {
      console.log('AUTH: no token in Bearer');
    }
    callback('Unauthorized');
  }
  const token = split[1].trim();
  /*
   * extra custom authorization logic here: OAUTH, JWT ... etc
   * search token in database and check if valid
   * here for demo purpose we will just compare with hardcoded value
   */
   switch (token.toLowerCase()) {
    case "4674cc54-bd05-11e7-abc4-cec278b6b50a":
      callback(null, generatePolicy('user123', 'Allow', event.methodArn));
      break;
    case "4674cc54-bd05-11e7-abc4-cec278b6b50b":
      callback(null, generatePolicy('user123', 'Deny', event.methodArn));
      break;
    default:
      callback('Unauthorized');
   }

}; 
```

如果令牌值为`674cc54-bd05-11e7-abc4-cec278b6b50a`，授权器函数返回指定方法的允许 IAM 策略。
这允许调用者调用指定的方法。呼叫者接收到 200 OK 响应。
如果授权令牌为`4674cc54-bd05-11e7-abc4-cec278b6b50b`，授权者功能返回针对指定方法的拒绝策略。
如果头中没有令牌或令牌无法识别，它会以 HTTP 代码 401“未授权”退出。

[这里的](https://github.com/piczmar/serverless-authorizers/tree/67fa5ef984184bd11b9de76f4bdc915545a67442)是完整的源代码(注意提交 ID)。

我们现在可以用 Curl 测试端点:

```
curl https://28p4ur5tx8.execute-api.us-east-1.amazonaws.com/dev/hello/rest

{"message":"Unauthorized"}

curl -H "Authorization:Bearer 4674cc54-bd05-11e7-abc4-cec278b6b50b" https://28p4ur5tx8.execute-api.us-east-1.amazonaws.com/dev/hello/rest

{"Message":"User is not authorized to access this resource with an explicit deny"}

curl -H "Authorization:Bearer 4674cc54-bd05-11e7-abc4-cec278b6b50a" https://28p4ur5tx8.execute-api.us-east-1.amazonaws.com/dev/hello/rest

{"message":"Hello REST, authenticated user: user123 !"} 
```

关于 [AWS 文档](http://docs.aws.amazon.com/apigateway/latest/developerguide/use-custom-authorizer.html)中自定义授权人的更多信息

在下一个无服务器授权系列文章中，我将解释 IAM 授权器以及我们如何授权 GraphQL 端点。

* * *

这篇文章最初发布在 [https://cloudly.tech](https://cloudly.tech/blog/serverless-authorizers-1/) 上，这是我关于无服务器技术和无服务器框架的博客。