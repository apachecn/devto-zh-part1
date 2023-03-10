# 在 RESTful 应用程序中使用 jwt 进行身份验证

> 原文：<https://dev.to/perrydbucs/using-jwts-for-authentication-in-restful-applications-55hc>

## 问题是

使用 MEAN stack 构建的应用程序通常在后端使用 Node、MongoDB 和 Express 来实现 RESTful 接口前端的业务逻辑。大部分工作在后端完成，Angular 作为 MVC(模型-视图-控制器)模式中的增强视图。将业务规则和逻辑保留在后端意味着应用程序是视图不可知的；从 Angular 切换到 React 或 straight jQuery 或 PHP 应该会产生相同的功能。

通常情况下，我们需要保护一些后端路由，使它们只对经过身份验证的用户可用。挑战在于我们的后端服务应该是无状态的，这意味着我们需要一种方法让前端代码在每次请求时提供身份验证的证据。同时，我们不能相信任何前端代码，因为它不在我们的控制之内。我们需要一个无可辩驳的机制来证明完全在后端管理的身份验证。我们还希望该机制不受客户端代码的控制，并以一种难以或不可能被欺骗的方式来实现。

## 解决方案

JSON Web 令牌(jwt)是满足这些需求的一个很好的解决方案。令牌基本上是由三部分组成的 JavaScript 对象:

*   包含用于生成令牌的算法信息的*报头*
*   具有一项或多项索赔的*机构*
*   基于报头和主体的加密签名

jwt 在 [RFC7519](https://tools.ietf.org/html/rfc7519) 中进行了正式描述。它们本身没有什么身份验证，它们是一种在双方之间封装和传输数据的机制，确保了信息的完整性。我们可以利用这一点给客户一种方法来证明他们的地位，而不涉及客户。流程是这样的:

1.  客户端通过服务器进行身份验证(或通过第三方，如 OAuth 提供者)
2.  服务器使用只有服务器知道的秘密创建描述身份验证状态和授权角色的签名 JWT
3.  服务器在标记为 *httpOnly* 的会话 cookie 中向客户端返回 JWT
4.  在每次请求时，客户端自动将 cookie 和附带的 JWT 发送给服务器
5.  服务器验证每个请求的 JWT，并决定是否允许客户端访问受保护的资源，返回重新请求的资源或错误状态

使用 cookie 来传输 JWT 提供了一种简单、自动化的方法来在客户端和服务器之间来回传递令牌，并使服务器能够控制 cookie 的生命周期。将 cookie *标记为 httpOnly* 意味着它对客户端功能不可用。而且，由于令牌是使用只有服务器知道的秘密来签名的，所以很难或不可能欺骗令牌中的声明。

本文中讨论的实现使用了一种简单的基于哈希的签名方法。JWT 的标头和正文是 Base64 编码的，然后对编码的标头和正文以及服务器端机密进行哈希处理以生成签名。另一种选择是使用公钥/私钥对来签名和验证 JWT。在本例中，JWT 只在服务器上处理，因此使用签名密钥没有任何好处。

## 代码中的 JWT 授权

让我们看一下实现我们工作流的一些代码。我在下面的例子中使用的应用程序依赖于来自 Twitter 的第三方 OAuth 认证，并且从一个会话到另一个会话为用户保留了最少的配置文件信息。身份验证成功后返回的 Twitter 访问令牌被用作 mongoDB 数据库中用户记录的密钥。该令牌将一直存在，直到用户注销或用户在关闭浏览器窗口后重新进行身份验证(从而使包含 JWT 的会话 cookie 失效)。注意，为了可读性，我简化了错误处理。

### 属国

以下代码示例中使用了两个便利包:

*   简化 cookie 处理的 cookie-parser - Express 中间件
*   jsonwebtoken——抽象 jwt 的签名和验证，基于 node-jws 包

我还用 Mongoose 作为 mongoDB 上面的一层；它通过模式提供 ODM，还提供了几种方便的查询方法。

### 创建 JWT 并放入会话 cookie 中

Twitter 的身份验证完成后，Twitter 调用应用程序上的回调方法，传回访问令牌和密码，以及用户的信息，如 Twitter ID 和屏幕名称(在*结果*对象中传递)。用户的相关信息存储在数据库文档中:

```
User.findOneAndUpdate( {twitterID: twitterID},
 {
     twitterID: twitterID,
     name: results.screen_name,
     username: results.screen_name,
     twitterAccessToken: oauth_access_token,
     twitterAccessTokenSecret: oauth_access_token_secret
 },
 {'upsert': 'true'},
 function (err, result) {
     if (err) {
     console.log(err)
 }
 else {
     console.log("Updated", results.screen_name, "in database.")
 }
})

```

*upsert* 选项指示 mongoDB 创建一个文档(如果它不存在),否则它更新一个现有的文档。

接下来，组装 JWT。jsonwebtoken 包负责创建 JWT 的头部，所以我们只需用 Twitter 访问令牌填充主体。在授权检查期间，我们将使用这个访问令牌在数据库中查找用户。

```
const jwtPayload = {
     twitterAccessToken: oauth_access_token
 }
```

然后签署 JWT。

```
 const authJwtToken = jwt.sign(jwtPayload, jwtConfig.jwtSecret)
```

jwtSecret 是一个字符串，可以是用于所有用户的单个值(就像在这个应用程序中一样)，也可以是每个用户的值，在这种情况下，它必须与用户记录一起存储。针对每个用户的秘密的策略可能是使用 Twitter 返回的 OAuth 访问令牌秘密，尽管如果来自 Twitter 的响应被拦截，这会带来一点风险。Twitter 秘密和服务器秘密的结合是一个不错的选择。当授权客户端的请求时，在验证签名的过程中使用该秘密。因为它存储在服务器上，从不与客户端共享，所以这是验证客户端提供的令牌实际上已由服务器签名的有效方法。

签名的 JWT 被放在饼干上。cookie 被标记为 *httpOnly* ，这限制了在客户端上的可见性，并且其到期时间被设置为零，使其成为仅会话 cookie。

```
const cookieOptions = {
  httpOnly: true,
  expires: 0 
 }
 res.cookie('twitterAccessJwt', authJwtToken, cookieOptions)
```

请记住，cookie 对客户端代码是不可见的，因此，如果您需要一种方法来告诉客户端用户已经过身份验证，那么您需要向另一个可见的 cookie 添加一个标志，或者将指示授权状态的数据传回客户端。

### 为什么一块饼干是 JWT 饼干？

我们当然可以将 JWT 作为普通对象发送回客户端，并使用它包含的数据来驱动客户端代码。有效负载没有加密，只是 Base64 编码，因此客户端可以访问。它可以放在会话上，用于与服务器之间的传输，尽管这必须在服务器和客户端的每个请求-响应对上完成，因为这种会话变量不会自动来回传递。

另一方面，*cookies*会随着每个请求和每个响应自动发送，无需任何额外的操作。只要 cookie 没有过期或被删除，它就会伴随每个请求返回到服务器。此外，标记 cookie *httpOnly* 对客户端代码隐藏了它，减少了它被篡改的机会。这个特殊的 cookie 只用于授权，所以客户端不需要看到它或与之交互。

## 授权请求

此时，我们已经向客户机提交了一个由服务器签名的授权令牌。每次客户端向后端 API 发出请求时，令牌都会在会话 cookie 中传递。请记住，服务器是无状态的，因此我们需要在每个请求上验证令牌的真实性。该过程有两个步骤:

1.  检查令牌上的签名，以证明令牌未被篡改
2.  验证与令牌相关联的用户是否在我们的数据库中
3.  [可选]检索该用户的一组角色

仅仅检查签名是不够的——这只是告诉我们令牌中的信息在离开服务器后没有被篡改，而不是所有者就是他们所说的那个人；攻击者可能已经窃取了 cookie 或者截获了它。第二步给我们一些保证，用户是有效的；数据库条目是在 Twitter OAuth 回调中创建的，这意味着用户刚刚通过 Twitter 认证。令牌本身位于会话 cookie 中，这意味着它不会在客户端持久化(它保存在内存中，而不是磁盘上)，并且设置了 *httpOnly* 标志，这限制了它在客户端上的可见性。

在 Express 中，我们可以创建一个验证受保护请求的中间件功能。并非所有请求都需要这种保护；应用程序的某些部分可能对未登录的用户开放。URI */db* 上的受限访问 POST 请求如下所示:

```
// POST Create a new user (only available to logged-in users) // router.post('/db', checkAuthorization, function (req, res, next) {
...
}
```

在这个路由中， *checkAuthorization* 是一个验证客户端发送的 JWT 的函数:

```
const checkAuthorization = function (req, res, next) {

    // 1\. See if there is a token on the request...if not, reject immediately
 //  const userJWT = req.cookies.twitterAccessJwt
    if (!userJWT) {
        res.send(401, 'Invalid or missing authorization token')
    }
```

```
    //2\. There's a token; see if it is a valid one and retrieve the payload
 //  else {
        const userJWTPayload = jwt.verify(userJWT, jwtConfig.jwtSecret)
        if (!userJWTPayload) {
            //Kill the token since it is invalid
 //  res.clearCookie('twitterAccessJwt')
            res.send(401, 'Invalid or missing authorization token')
        }
        else {
```

```
            //3\. There's a valid token...see if it is one we have in the db as a logged-in user
 //  User.findOne({'twitterAccessToken': userJWTPayload.twitterAccessToken})
                .then(function (user) {
                    if (!user) {
                        res.send(401, 'User not currently logged in')
                    }
                    else {
                        console.log('Valid user:', user.name)
                        next()
                    }

                })
        }
    }
}
```

假设授权 cookie 存在(步骤 1)，然后使用存储在服务器上的秘密检查它的有效签名(步骤 2)。如果签名有效，jwt.verify 返回 jwt 有效载荷对象，否则返回 null。缺失或无效的 cookie 或 JWT 会导致对客户端的 401(未授权)响应，并且在无效 JWT 的情况下，cookie 本身会被删除。

如果步骤 1 和 2 有效，我们将使用 Twitter 访问令牌作为密钥，检查数据库以查看我们是否有 JWT 上携带的访问令牌的记录。如果记录存在，这很好地表明客户端得到了授权，并且在步骤 3 结束时对 *next()* 的调用将控制传递给中间件链中的下一个函数，在本例中是 POST 路由的剩余部分。

## 注销用户

如果用户显式注销，将调用一个后端路由来完成这项工作:

```
//This route logs the user out: //1\. Delete the cookie //2\. Delete the access key and secret from the user record in mongo // router.get('/logout', checkAuthorization, function (req, res, next) {
    const userJWT = req.cookies.twitterAccessJwt
    const userJWTPayload = jwt.verify(userJWT, jwtConfig.jwtSecret)

    res.clearCookie('twitterAccessJwt')
    User.findOneAndUpdate({twitterAccessToken: userJWTPayload.twitterAccessToken},
        {
            twitterAccessToken: null,
            twitterAccessTokenSecret: null
  },
        function (err, result) {
            if (err) {
                console.log(err)
            }
            else {
                console.log("Deleted access token for", result.name)
            }
            res.render('twitterAccount', {loggedIn: false})
        })

})
```

我们再次检查用户是否登录，因为我们需要 JWT 的验证内容来更新用户的数据库记录。

如果用户只是关闭浏览器选项卡而没有注销，则包含 JWT 的会话 cookie 将在客户端上被删除。下次访问时，JWT 不会在 checkAuthorization 中进行验证，用户将被定向到登录页面；成功登录将更新数据库中的访问令牌和相关密码。

## 评论

排名不分先后...

一些服务对访问令牌设置了较短的到期时间，并提供了用“刷新”令牌交换新访问令牌的方法。在这种情况下，需要一个额外的步骤来更新存储在会话 cookie 中的令牌。因为对第三方服务的访问是在服务器上处理的，所以这对客户端是透明的。

这个应用程序只有一个角色:登录用户。对于需要多个角色的应用程序，它们应该存储在数据库中，并在每次请求时检索。

出现了一个与 checkAuthorization 相关的体系结构问题。问题是，谁应该负责处理一个无效用户？实际上， *checkAuthorization* 应该返回一个可以被每个受保护路由使用的布尔值吗？让 *checkAuthorization* 处理无效案例集中了这种行为，但代价是失去了路由的灵活性。在这一点上，我是双向的...未经授权的用户就是未经授权的，句号，所以在 checkAuthorization 中处理这个函数是有意义的；但是，可能会有这样的用例:路由为未经身份验证的用户传回数据的子集，或者为经过授权的用户添加额外的信息。对于这个特殊的例子，集中式版本工作得很好，但是您将希望基于您赢得的用例来评估这种方法。

本例中的路由只是呈现一个 Pug 模板，显示用户的 Twitter 帐户信息，一个标志(loggedIn)用于显示和隐藏 UI 组件。一个更复杂的应用程序需要一种更清晰的方式让客户端知道用户的状态。

带有 sampe 代码的 gist 可从[获得 gist:BDB 91 ed 5 f 7d 87 C5 f 79 a 74d 3 B4 d 978d 3d](https://gist.github.com/pdonham/bdb91ed5f7d87c5f79a74d3b4d978d3d)

*最初在我的博客上发布[在波斯顿大学](http://sites.bu.edu/perryd/?p=259&preview=true)*