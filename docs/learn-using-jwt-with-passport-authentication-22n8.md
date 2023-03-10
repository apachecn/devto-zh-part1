# 了解如何使用 JWT 进行 Passport 身份验证

> 原文：<https://dev.to/_arpy/learn-using-jwt-with-passport-authentication-22n8>

[![](img/fd752f682a6bcdbdca8e180f117054fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8BnElxAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AQTeLq8g_qQ-IL8ry7pBwrg.jpeg)

### 简介

现在几乎每个 web 和手机 app 都有*认证*。他们中的大多数提供不同的登录方式，如脸书，谷歌或电子邮件/密码一次。

[Passport](http://www.passportjs.org/) 是一个 Node.js 中间件，提供了多种不同的请求认证策略，易于实现。默认情况下，它将用户对象存储在会话中。

[JSON Web Tokens](https://jwt.io/) 是一种认证标准，它通过在请求中分配和传递加密令牌来帮助识别登录用户，而不是将用户存储在服务器上的会话中并创建 cookie。它有不同的集成，包括一个 [Node.js 模块](https://github.com/auth0/node-jsonwebtoken)。

下面是一个关于结合使用这两个模块并在基于 express 的后端设置认证的教程。幸运的是， **Passport** 允许在请求中存储用户对象*，而不是会话。*

本教程将使用简单的本地(电子邮件/密码)认证，但它也可以与任何其他策略一起使用。

首先，让我们安装依赖项。

```
npm install --save passport passport-local passport-jwt jsonwebtoken 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都是这样进行的:

*   当用户登录时，后端创建一个签名的令牌并返回它作为响应
*   客户端将令牌保存在本地(通常保存在 **localStorage** 中),并在需要认证的每个后续请求中将其发回
*   所有需要身份验证的请求都要通过一个中间件，该中间件会检查所提供的令牌，只有在令牌得到验证的情况下才允许请求

所以，我们来实现这个逻辑。

### 登录

假设我们已经在 ***app.js*** 旁边的单独文件中设置并使用了本地护照策略，如下:

```
//passport.js

const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;

passport.use(new LocalStrategy({
        usernameField: 'email',
        passwordField: 'password'
    }, 
    function (email, password, cb) {

//this one is typically a DB call. Assume that the returned user object is pre-formatted and ready for storing in JWT

return UserModel.findOne({email, password})
           .then(user => {
               if (!user) {
                   return cb(null, false, {message: 'Incorrect email or password.'});
               }

return cb(null, user, {message: 'Logged In Successfully'});
          })
          .catch(err => cb(err));
    }
)); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要要求这个文件在 ***app.js.***

```
//app.js

const express = require('express');
...
require('./passport');

const app = express();
...
const auth = require('./routes/auth');
app.use('/auth', auth); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们的 ***auth.js*** route 文件中，我们将实现登录动作。这里，我们使用本地策略调用 passport 身份验证函数，处理错误并登录用户。

```
//routes/auth.js

const express = require('express');
const router = express.Router();
const jwt = require('jsonwebtoken');
const passport = require("passport”);

/* POST login. */
router.post('/login', function (req, res, next) {

passport.authenticate('local', {session: false}, (err, user, info) => {
        if (err || !user) {
            return res.status(400).json({
                message: 'Something is not right',
                user : user
            });
        }

req.login(user, {session: false}, (err) => {
           if (err) {
               res.send(err);
           }

// generate a signed son web token with the contents of user object and return it in the response

const token = jwt.sign(user, 'your_jwt_secret');
           return res.json({user, token});
        });
    })(req, res);
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在 passport 选项中传递了 **{session: false}** ，因此它不会在会话中保存用户。此外，我们基于用户对象创建并返回一个*签名的 JSON web 令牌*给客户端。当然，您可以选择任何对象来创建令牌，只要它能帮助您识别您的用户。其思想是，存储您可以使用的最少信息，而不必在所有经过身份验证的请求中从数据库中检索用户。

### 受保护的请求

现在，我们将创建一个中间件，它只允许具有有效令牌的请求访问一些需要认证的特殊路由，例如 */user/profile。*为此，我们将使用 ***passport-jwt*** 策略。我们将把它添加到我们的 ***passport.js*** 文件中。

```
//passport.js

...
const passportJWT = require("passport-jwt");
const JWTStrategy = passportJWT.Strategy;
const ExtractJWT = passportJWT.ExtractJwt;
...

passport.use(new JWTStrategy({
        jwtFromRequest: ExtractJWT.fromAuthHeaderAsBearerToken(),
        secretOrKey : 'your_jwt_secret'
    },
    function (jwtPayload, cb) {

        //find the user in db if needed. This functionality may be omitted if you store everything you'll need in JWT payload.
        return UserModel.findOneById(jwtPayload.id)
            .then(user => {
                return cb(null, user);
            })
            .catch(err => {
                return cb(err);
            });
    }
)); 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们假设客户端将在**授权报头中发送 JWT 令牌作为不记名令牌。**Passport JWT 策略[支持从请求中获取令牌的许多其他方式](https://github.com/themikenicholson/passport-jwt)。选择适合你需要的。

现在，我们需要做的就是在我们的应用程序中为受保护的路线使用这个中间件。对于本教程，我们将准备一个简单的用户路径，如下所示:

```
//routes/user.js

const express = require('express');
const router = express.Router();

/* GET users listing. */
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});

/* GET user profile. */
router.get('/profile', function(req, res, next) {
    res.send(req.user);
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

并在用户路由上使用 passport 认证中间件，如下所示:

```
//app.js

const express = require('express');
...
require('./passport');

const app = express();
...
const auth = require('./routes/auth');
const user = require('./routes/user');

app.use('/auth', auth);
app.use('/user', passport.authenticate('jwt', {session: false}), user); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

继续尝试一些请求，现在它们将得到带有 Passport 的 JSON Web 令牌授权的支持👍

* * *