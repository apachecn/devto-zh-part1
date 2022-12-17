# 用于服务静态文件的 Koa 中间件

> 原文：<https://dev.to/adamkdean/koa-middleware-for-serving-static-files-458o>

我经常发现自己在项目中反复使用相同的代码片段来提供静态文件，作为使用 Koa 的项目的一部分。今天我超过了复制粘贴的限制，所以我把它打包到一个名为`koa-serve`的模块中。

这可能比`koa-static`简单得多，但对于我想要和需要的东西来说，它工作得很好。

```
var koa = require('koa'),
    serve = require('koa-serve'),
    app = koa();

app.use(serve('assets'));
app.listen(8000); 
```

如果不是`__dirname`，你也可以定义你的根目录在哪里。

```
var koa = require('koa'),
    serve = require('koa-serve'),
    app = koa();

app.use(serve('assets', '/path/to/your/root'));
app.listen(8000); 
```

例如，如果您的客户机文件位于父目录中，而 index.js 位于 server/中，例如:

```
var koa = require('koa'),

    serve = require('koa-serve'),

    path = require('path'),

    app = koa();

app.use(serve('assets '，path.join(__dirname，' .. ')，‘客户端’))；
app . listen(8000)； 
```

## 
  
安装它

```
npm install koa-serve 
```

## 
  
更多链接

GitHub:[https://github.com/adamkdean/koa-serve](https://github.com/adamkdean/koa-serve)
T3】NPM:[https://www.npmjs.com/package/koa-serve](https://www.npmjs.com/package/koa-serve)