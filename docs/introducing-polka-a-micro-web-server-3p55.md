# 介绍波尔卡:一个微型网络服务器。

> 原文：<https://dev.to/kvng_zeez/introducing-polka-a-micro-web-server-3p55>

[![polka](img/3f954ed3b9c261d05e2a3d407bd61dc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gLlb2R-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qz07w2nqfdarbkbpfs93.png)

# 什么是波尔卡？

波尔卡是一种极其简约、高性能的 Express.js 替代方案。

## 安装。

```
npm i --save polka 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

在路由和 API 调用方面，Polka 与 ExpressJS 有相似的模式。

### 简单的例子。

```
const polka = require('polka');

polka()
  .get('/', (req, res) => {
      res.end('Hello there !');
  })
  .listen(9000).then(_ => {
    console.log(`> Running on localhost:3000`);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 波尔卡的 API

波尔卡有四个主要的 API 方法。

*   **波尔卡(选项)** -返回波尔卡的一个实例。
*   **使用(基础、..fn)**
*   **parse(req)**
*   **监听(端口，主机名)**
*   **处理程序(请求、结果、已解析)**

## 用波尔卡路由。

路由用于定义应用程序如何响应不同的 HTTP 方法和端点。

### 基础知识

每条路线都由一个[路径模式](#patterns)、一个 [HTTP 方法](#methods)和一个[处理程序](#handlers)(也就是您想要做的事情)组成。

在代码中，这看起来像:

```
app.METHOD(pattern, handler); 
```

Enter fullscreen mode Exit fullscreen mode

其中:

*   `app`是`polka`的实例* `method`是任何有效的 HTTP 方法，小写
*   `pattern`是一个路由模式(字符串)
*   `handler`是匹配`pattern`时要执行的功能

此外，单个路径名(或`pattern`)可以在多种方法中重复使用。

以下示例演示了一些简单的路由。

```
const app = polka();

app.get('/', (req, res) => {
  res.end('Hello world!');
});

app.get('/users', (req, res) => {
  res.end('Get all users!');
});

app.post('/users', (req, res) => {
  res.end('Create a new User!');
});

app.put('/users/:id', (req, res) => {
  res.end(`Update User with ID of ${req.params.id}`);
});

app.delete('/users/:id', (req, res) => {
  res.end(`CY@ User ${req.params.id}!`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 图案

与非常流行的 [`path-to-regexp`](https://github.com/pillarjs/path-to-regexp) 不同，波尔卡使用字符串比较来定位路线匹配。虽然[更快](https://github.com/lukeed/matchit#benchmarks)更高效&更内存，这也确实防止了复杂的模式匹配。

但是，不要害怕！💥支持所有基本的和最常用的模式。您可能只在第一时间使用过这些模式。😉

> 波尔卡不支持的基于`RegExp`的模式列表见 [`comparison`](#comparions) 。

支持的模式类型有:

*   静态(`/users`)
*   命名参数(`/users/:id`)
*   嵌套参数(`/users/:id/books/:title`)
*   可选参数(`/users/:id?/books/:title?`)
*   任何匹配/通配符(`/users/*`)

### 参数

路线`pattern`中包含的任何命名参数将自动添加到您的传入`req`对象中。所有的参数都将在`req.params`中找到，并以相同的名字命名。

> **重要提示:**您的参数名称应该是唯一的，因为共享名称会相互覆盖！

```
app.get('/users/:id/books/:title', (req, res) => {
  let { id, title } = req.params;
  res.end(`User: ${id} && Book: ${title}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl /users/123/books/Narnia
#=> User: 123 && Book: Narnia 
```

Enter fullscreen mode Exit fullscreen mode

## 方法

支持任何有效的 HTTP 方法！然而，出于演示目的，本文中只使用了最常见的方法。

> **注:**有效方法的完整列表，请参见[该列表](http://expressjs.com/en/4x/api.html#routing-methods)。

## 经手人

请求处理者接受传入的 [`ClientRequest`](https://nodejs.org/dist/latest-v9.x/docs/api/http.html#http_class_http_clientrequest) 和制定的 [`ServerResponse`](https://nodejs.org/dist/latest-v9.x/docs/api/http.html#http_class_http_serverresponse) 。

每个路由定义必须包含一个有效的`handler`函数，否则在运行时将会抛出一个错误。

> **重要:**你必须*永远*终止一个`ServerResponse`！

这是一个**非常好的**实践，在一个处理程序中*总是*终止你的响应( [`res.end`](https://nodejs.org/api/http.html#http_request_end_data_encoding_callback) )，即使你期望一个中间件为你做这件事。如果响应被终止/未被终止，服务器将挂起&并最终退出，出现`TIMEOUT`错误。

> **注意:**这是一种本土的`http`行为。

#### 异步处理器

如果使用 Node 7.4 或更高版本，您可以利用本机的`async`和`await`语法！😻

不需要特别的准备—只需添加适当的关键字。

```
const app = polka();

const sleep = ms => new Promise(r => setTimeout(r, ms));

async function authenticate(req, res, next) {
  let token = req.getHeader('authorization');
  if (!token) return app.send(res, 401);
  req.user = await Users.find(token); // <== fake
  next(); // done, woot!
}

app
  .use(authenticate)
  .get('/', async (req, res) => {
    // log middleware's findings
    console.log('~> current user', req.user);
    // force sleep, because we can~!
    await sleep(500);
    // send greeting
    res.end(`Hello, ${req.user.name}`);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 中间件

中间件是运行在接收请求和执行路由的`handler`响应之间的功能。

> 从快递过来的？使用任何你已经知道和喜欢的中间件！🎉

中间件签名接收请求(`req`)、响应(`res`)和回调(`next`)。

这些可以对`req`和`res`对象应用突变，并且与 Express 不同，可以访问`req.params`、`req.pathname`、`req.search`和`req.query`！

最重要的是，一个中间件 ***必须*** 要么调用`next()`，要么终止响应(`res.end`)。做不到这一点将导致永无止境的回应，最终导致`http.Server`崩溃。

```
// Log every request
function logger(req, res, next) {
  console.log(`~> Received ${req.method} on ${req.url}`);
  next(); // move on
}

function authorize(req, res, next) {
  // mutate req; available later
  req.token = req.getHeader('authorization');
  req.token ? next() : ((res.statusCode=401) && res.end('No token!'));
}

polka().use(logger, authorize).get('*', (req, res) => {
  console.log(`~> user token: ${req.token}`);
  res.end('Hello, valid user');
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl /
# ~> Received GET on /
#=> (401) No token!

$ curl -H "authorization: secret" /foobar
# ~> Received GET on /foobar
# ~> user token: secret
#=> (200) Hello, valid user 
```

Enter fullscreen mode Exit fullscreen mode

在 Polka 中，中间件功能是全局安装的，这意味着它们将根据每个请求运行。相反，您必须应用内部过滤器来决定您的中间件应该在何时何地运行。

> 注意:这可能会在波尔卡 1.0 中改变🤔

```
function foobar(req, res, next) {
  if (req.pathname.startsWith('/users')) {
    // do something magical
  }
  next();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 中间件错误

如果中间件出现错误，将退出循环。这意味着没有其他中间件会执行&路由处理程序也不会。

类似地，不管`statusCode`如何，提前响应终止也将退出循环&，阻止路由处理器运行。

有三种方法可以从中间件函数中“抛出”错误。

> **提示:**都不用`throw`

1.  * *将任何字符串传递给`next()` **

    这将退出循环并发送一个`500`状态代码，以您的错误字符串作为响应体。

```
 polka()
      .use((req, res, next) => next('💩'))
      .get('*', (req, res) => res.end('wont run')); 
```

Enter fullscreen mode Exit fullscreen mode

```
 $ curl /
    #=> (500) 
```

Enter fullscreen mode Exit fullscreen mode

1.  **传一个`Error`到`next()`T3】**

    这类似于上面的选项，但是给你一个窗口将`statusCode`改变为默认的`500`以外的值。

```
function oopsies(req, res, next) {
  let err = new Error('Try again');
  err.code = 422;
  next(err);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 $ curl /
    #=> (422) Try again 
```

Enter fullscreen mode Exit fullscreen mode

1.  **提前终止响应**

    一旦响应结束，就没有理由继续循环！

    这种方法是最通用的，因为它允许控制传出`res`的每个方面。

```
 function oopsies(req, res, next) {
      if (true) {
        // something bad happened~
        res.writeHead(400, {
          'Content-Type': 'application/json',
          'X-Error-Code': 'Please dont do this IRL'
        });
        let json = JSON.stringify({ error:'Missing CSRF token' });
        res.end(json);
      } else {
        next(); // never called FYI
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl /
    #=> (400) {"error":"Missing CSRF token"} 
```

Enter fullscreen mode Exit fullscreen mode

## 基准

一轮跨不同节点版本的 Polka-vs-Express 基准测试可以在这里找到。

> **重要:**时间大多花在*你的申请码*而不是快递或波尔卡码上！从快速切换到波尔卡(可能)不会显示出如此大的性能增益。

```
Node 8.9.0

Native
    Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     2.24ms  112.34us   5.57ms   92.15%
        Req/Sec     5.38k    99.48     5.57k    81.81%
      432562 requests in 10.10s, 42.90MB read
    Requests/sec:  42815.14
    Transfer/sec:      4.25MB

Polka
    Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     2.26ms  115.55us   5.19ms   87.16%
        Req/Sec     5.32k    97.34     5.55k    72.77%
      428208 requests in 10.10s, 42.47MB read
    Requests/sec:  42388.92
    Transfer/sec:      4.20MB

Express
    Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     5.15ms  421.69us   8.51ms   77.95%
        Req/Sec     2.34k    77.06     2.55k    72.12%
      186390 requests in 10.01s, 36.97MB read
    Requests/sec:  18628.36
    Transfer/sec:      3.70MB

Fastify
    Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     2.91ms  201.13us   7.51ms   58.07%
        Req/Sec     4.14k   130.04     4.48k    65.59%
      333158 requests in 10.10s, 41.30MB read
    Requests/sec:  32979.84
    Transfer/sec:      4.09MB

Koa
    Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     3.43ms  369.96us   8.67ms   87.30%
        Req/Sec     3.51k   114.78     4.12k    69.76%
      281808 requests in 10.10s, 38.97MB read
    Requests/sec:  27892.99
    Transfer/sec:      3.86MB 
```

Enter fullscreen mode Exit fullscreen mode

## 比较

波尔卡的 API 旨在与 Express 非常相似，因为大多数 Node.js 开发人员已经熟悉了它。如果你知道快速，你已经知道波尔卡！💃

然而，有几个主要的区别。波尔卡不支持或不提供:

1) **任何内置的视图/渲染引擎。**
大多数模板引擎可以集成到中间件功能中，或者直接在路由处理器中使用。

2) **从中间件内部`throw`的能力。**
然而，所有其他形式的中间件-错误都被支持。(参见中间件选项)

```
 function middleware(res, res, next) {
  // pass an error message to next()
  next('uh oh');

  // pass an Error to next()
  next(new Error('🙀'));

  // send an early, customized error response
  res.statusCode = 401;
  res.end('Who are you?');
} 
```

Enter fullscreen mode Exit fullscreen mode

3) **类似快递的响应助手...还没！(#14)**
快递有一套不错的[响应帮手](http://expressjs.com/en/4x/api.html#res.append)。虽然 Polka 依赖于 [native Node.js 响应方法](https://nodejs.org/dist/latest-v9.x/docs/api/http.html#http_class_http_serverresponse)，但是附加一个包含一组类似助手的全局中间件是非常容易/可能的。( *TODO* )

4) **`RegExp`为基础的路线模式。**
波尔卡的路由器使用字符串比较来匹配路径和模式。这要快得多&更有效率。
以下路由模式**不被**支持:

```
 app.get('/ab?cd', _ => {});
app.get('/ab+cd', _ => {});
app.get('/ab*cd', _ => {});
app.get('/ab(cd)?e', _ => {});
app.get(/a/, _ => {});
app.get(/.*fly$/, _ => {}); 
```

Enter fullscreen mode Exit fullscreen mode

以下路由模式**受**支持:

```
 app.get('/users', _ => {});
    app.get('/users/:id', _ => {});
    app.get('/users/:id?', _ => {});
    app.get('/users/:id/books/:title', _ => {});
    app.get('/users/*', _ => {}); 
```

Enter fullscreen mode Exit fullscreen mode

## 学分。

所有的荣誉都归于[卢克·爱德华兹](http://twitter.com/lukeed05)出色的作品。