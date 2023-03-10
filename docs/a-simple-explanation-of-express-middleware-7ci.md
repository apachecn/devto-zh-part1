# 快速中间件浅释

> 原文：<https://dev.to/abelagoi/a-simple-explanation-of-express-middleware-7ci>

### 基本理念:

web 服务器可以被看作是接收请求并输出响应的功能。在传入的请求产生输出之后，函数是否在中间执行，该输出可以是传递的最终输出，或者可以由下一个中间件使用，直到循环完成，这意味着我们可以有多个中间件，它们将按照声明的顺序执行。下面的`middleware A`将在`middleware B`之前执行，`middleware B`将在`middleware C.`之前执行。我们可以将变量从一个中间件传递到另一个中间件。

* * *

[![](img/1dd821013b834876c1c1101b64bd32b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f_yFVwLw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AWTcjbYL3fmFw5XW6fq408g.png)

### 中间件用法:

要设置一个中间件，您可以为您想要添加的每个中间件层调用`app.use()`。中间件可以通用于所有路径，也可以只在服务器处理的特定路径上触发。下面是一个中间件声明的例子。

```
var app = express();
app.use(function () {}) //added to all paths or globally
app.get('/someroute', function() {}) //added to a specific path 
```

Enter fullscreen mode Exit fullscreen mode

### 仔细看看中间件:

当您使用`app.use('/some_route', myCallBack()).` Express 时，它将监听对该路线的请求，当它被点击时，它将调用您提供的函数，并给它三个参数:请求、响应和下一个(实际上是四个，但现在让事情简单些)。

您的回调可以这样定义:

```
function myCallback(a, b, c) {} //sample 1 
```

Enter fullscreen mode Exit fullscreen mode

或者像这样:

```
function myCallback(req, res, next) {} //sample 2 
```

Enter fullscreen mode Exit fullscreen mode

或者干脆

```
function myCallback(){} //sample 3 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例 1 中，您可以通过使用参数`a`来访问请求。在您的示例 2 中，您将通过使用`req`参数来访问它。在第三种情况下，你可以使用`arguments[0]`。这些参数是**请求**、**响应**和**下一个**。无论你是否调用它们，express 都会在内部调用你提供的函数:

```
function myCallback(requestObject, responseObject, nextMiddleware) {} 
```

Enter fullscreen mode Exit fullscreen mode

`requestObject:`包含关于 HTTP 请求的信息。您可以在`requestObject`中访问请求头、完整 url、呼叫者 IP 地址等。

`responseObject:`用于处理 requestObject。`responseObject`表示 Express 应用程序在收到 HTTP 请求时发送的 HTTP 响应。

`next`:可以接受参数，也可以不接受。当它不接受参数时，意味着转到下一个可执行文件。这是一种逃避中间件功能的方式。当你将参数传递给下一个函数，即`next(parameter)`时，你是在告诉 express 中间件发生了错误。有时候你需要这样做，我们马上就要去那里了。

### 中间件的酷用例:

我们可以在应用程序中使用许多在线中间件来修改请求，即`app.use(express.bodyParser())`、`app.use(express.cookieParser())`，但我们也可以在应用程序中使用定制中间件，例如:

**路由:**中间件可以用来在我们的 express 应用程序中定义不同的路由

```
app.get('home', function (req, res) { 
    res.render('home'); //when i visit home url, render the home view
});

app.post('another-route', function (req, res) { 
    res.sendStatus(200);
}); 
```

Enter fullscreen mode Exit fullscreen mode

**错误处理:**在 express 中有一个中间件表示为`Error Middleware`。该中间件采用如下四(4)个参数:

```
function (error, req, res, next) {} 
```

Enter fullscreen mode Exit fullscreen mode

如果 Express 发现一个中间件有四(4)个参数，而不是两个或三个，它将扫描我们所有的函数。它将中间件表示为错误中间件，这意味着它将允许您访问之前任何中间件抛出的任何错误，记得我之前说过，当您将参数传递到我们中间件中的`next(err)`函数时，这表示该中间件中发生了错误。正是这个错误中间件将能够访问从它之前的任何中间件抛出的任何错误。

```
app.get('home', function (req, res) { 
    res.render('home');
});

app.post('another-route', function (req, res, next) { 
    try {
        //something 
    } catch (err) { 
        next(err); 
    }
});

//below is use to handle general error within our application
app.use(function (error, req, res, next) { 
    res.locals.error=err;
        res.render("error-page");
}); 
```

Enter fullscreen mode Exit fullscreen mode

**保护特定的路线:**我们可以使用中间件来保护我们的应用程序中的特定路线。举个例子，我有一个仪表板页面，我只希望有会话的用户(已经登录)能够访问该页面，我可以使用中间件来阻止它，如下:

```
//guard
let requiresLogin = function(req, res, next) { 
    if (! req.session.loggedIn) { 
        err = new Error("Not authorized"); 
        next(err); 
     } 
     return next();
};

//protected route
app.get('dashboard', requiresLogin, function (req, res) { 
    res.render('home');
});

//general error handler
app.use(function (error, req, res, next) { 
    res.locals.error = err; //get error thrown from another-route above
        res.render("error-page");
}); 
```

Enter fullscreen mode Exit fullscreen mode

我很高兴与你分享我对 express 中间件的了解。如果你喜欢这篇文章，请鼓掌表示支持👏*。感谢您的宝贵时间，请务必关注我或在*下方留下您的评论👇