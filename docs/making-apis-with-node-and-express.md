# 用 Node 和 Express 制作 API

> 原文：<https://dev.to/albertofdzm/making-apis-with-node-and-express>

我准备用[节点](https://nodejs.org/en/)和 [Express](http://expressjs.com/) 做一个简单的 API，具体是一个 TODOs app 的 API。

*本文最初发表于[我的博客，onlythepixel.com](http://onlythepixel.com/2016/12/11/making-apis-with-node-and-express/)T3*

## 项目样板文件

因此，我在终端
中启动了一个名为`another-todo-api`的新节点项目

```
mkdir another-todo-api && cd $_
git init
echo 'Another boring TODO API' > README.md
npm init -y
echo 'node_modules
*.log' >> .gitignore
npm i -S express
git add .
git commit -m 'First commit' 
```

Enter fullscreen mode Exit fullscreen mode

***注:** `npm i -S`与`npm install --save`相同但方式更短。*

简单！我用一个空的 README 文件和一个新的 npm 包启动了一个新的 git repo，该包将 express 作为一个依赖项。让我们用 Express 玩一会儿。

我喜欢把我所有的源代码放在一个`src`文件夹中:

```
mkdir src
touch src/index.js 
```

Enter fullscreen mode Exit fullscreen mode

### `src/index.js`

```
const express = require('express')
const app = express()

module.exports = app 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**由于这篇文章很酷，所有 javascript 代码都将在 ES2015 中显示(所以建议使用 Node v6 或更高版本)，我将使用[标准代码风格](http://standardjs.com/index.html)来显示 javascript。*

现在要运行服务器，我不喜欢直接从`index.js`文件启动它，相反，我更喜欢通过外部 bin 文件运行它(就像 [Express 在其生成器](https://github.com/expressjs/generator/blob/d07ce53595086dd07efb100279a7b7addc059418/templates/js/www)中所做的那样)。

### `bin/www`

```
#!/usr/bin/env node /**
 * Created from https://github.com/expressjs/generator/blob/d07ce53595086dd07efb100279a7b7addc059418/templates/js/www
 */

/**
 * Module dependencies.
 */
const http = require('http')
const debug = require('debug')('another-todo:server')
const app = require('../src')

/**
 * Get port from environment and store in Express.
 */

const port = normalizePort(process.env.PORT || '3000')
app.set('port', port)

/**
 * Create HTTP server.
 */
const server = http.createServer(app)

/**
 * Normalize a port into a number, string, or false.
 */
function normalizePort (val) {
  const port = parseInt(val, 10)

  // named pipe
  if (isNaN(port)) return val

  // port number
  if (port >= 0) return port

  return false
}

/**
 * Event listener for HTTP server "error" event.
 */
function onError (error) {
  if (error.syscall !== 'listen') throw error

  const bind = typeof port === 'string'
    ? 'Pipe ' + port
    : 'Port ' + port

  // handle specific listen errors with friendly messages
  switch (error.code) {
    case 'EACCES':
      console.error(bind + ' requires elevated privileges')
      process.exit(1)
      break
    case 'EADDRINUSE':
      console.error(bind + ' is already in use')
      process.exit(1)
      break
    default:
      throw error
  }
}

/**
 * Event listener for HTTP server "listening" event.
 */
function onListening () {
  const addr = server.address()
  const bind = typeof addr === 'string'
    ? 'pipe ' + addr
    : 'port ' + addr.port
  debug('Listening on ' + bind)
}

/**
 * Listen on provided port, on all network interfaces.
 */
server.listen(port)
server.on('error', onError)
server.on('listening', onListening) 
```

Enter fullscreen mode Exit fullscreen mode

然后将这个文件绑定到我的 npm 脚本。

### `package.json`

```
 ...  "scripts":  {  "start":  "set DEBUG=another-todo:* && node bin/www",  "test":  "echo \"Error: no test specified\" && exit 1"  },  ... 
```

Enter fullscreen mode Exit fullscreen mode

我还需要包 [`debug`](https://www.npmjs.com/package/debug) 作为我项目的依赖项，因为我要在我的`www`文件:
中使用它

```
npm i -S debug 
```

Enter fullscreen mode Exit fullscreen mode

之后，我可以尝试我的全新快速服务器:

```
npm start

> another-todo-api@0.0.0 start /develop/another-todo-api
> set DEBUG=another-todo:* && node bin/www

  another-todo:server Listening on port 3000 +0ms 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，这个小家伙应该在我电脑的端口`3000`上监听。如果我用某个浏览器访问 [`http://localhost:3000`](http://localhost:3000) ，我会收到一个悲伤的`Cannot GET /`。

## [快速路由器](http://expressjs.com/en/4x/api.html#router)

是时候让这个家伙有一些声音，能够在我要求什么的时候回答我了。为此，我将使用快速路由器来构建我的 TODO API 片段。

### `src/v1/index.js`

```
const router = require('express').Router()

router.route('/')
  .get((req, res, next) => {
    return res.json({
      message: 'Let\'s TODO!'
    })
  })

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

***注:**`v1`的那个东西是因为在 API 中实现版本系统是一个很好的实践。*

只是对一个 GET 请求的简单回复，如果我再去 [`http://localhost:3000`](http://localhost:3000) ，什么都不会发生...因为我需要在我的 Express 应用程序中安装这个路由器路径。

### `src/index.js`

```
const express = require('express')
const app = express()
const v1 = require('./v1')

/**
 * Routes
 */
app.use('/v1', v1)

module.exports = app 
```

Enter fullscreen mode Exit fullscreen mode

这将工作得很好！如果我访问 [`http://localhost:3000/v1`](http://localhost:3000/v1) 这个东西现在会有声音:

```
{"message":"Let's TODO!"} 
```

Enter fullscreen mode Exit fullscreen mode

## [中间件](http://expressjs.com/en/guide/using-middleware.html)

现在我要加一些中间件，避免接触不支持 JSON 格式的系统。

### `src/index.js`

```
const express = require('express')  
const app = express()  
const v1 = require('./v1')

/**
 * Ensure JSON acceptance
 */
app.use((req, res, next) => {
  let err

  if (!req.accepts('json')) {
    err = new Error('Not Acceptable')
    err.status = 406
  }

  return next(err)
})

/**
 * Routes
 */
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个返回错误的中间件，我可以用 [`curl`](https://curl.haxx.se/) 测试它(可能你的终端已经有了)。

```
curl -i -H "Accept: text" localhost:3000

HTTP/1.1 406 Not Acceptable
X-Powered-By: Express
X-Content-Type-Options: nosniff
Content-Type: text/html; charset=utf-8
Content-Length: 1052
Date: Sun, 11 Dec 2016 18:40:03 GMT
Connection: keep-alive

Error: Not Acceptable<br> &nbsp; &nbsp;at app.use (/develop/another-todo-api/src/index.js:9:11)<br> &nbsp; &nbsp;at Layer.handle [as handle_request] (/develop/another-todo-api/node_modules/express/lib/router/layer.js:95:5)<br> &nbsp; &nbsp;at trim_prefix (/develop/another-todo-api/node_modules/express/lib/router/index.js:312:13)<br> &nbsp; &nbsp;at /develop/another-todo-api/node_modules/express/lib/router/index.js:280:7<br> &nbsp; &nbsp;at Function.process_params (/develop/another-todo-api/node_modules/express/lib/router/index.js:330:12)<br> &nbsp; &nbsp;at next (/develop/another-todo-api/node_modules/express/lib/router/index.js:271:10)<br> &nbsp; &nbsp;at expressInit (/develop/another-todo-api/node_modules/express/lib/middleware/init.js:33:5)<br> &nbsp; &nbsp;at Layer.handle [as handle_request] (/develop/another-todo-api/node_modules/express/lib/router/layer.js:95:5)<br> &nbsp; &nbsp;at trim_prefix (/develop/another-todo-api/node_modules/express/lib/router/index.js:312:13)<br> &nbsp; &nbsp;at /develop/another-todo-api/node_modules/express/lib/router/index.js:280:7 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**如果我尝试不带`--header "Accept: text"`的话，它会以正确的响应回复我。*

年轻人，注意你的语言！它在`HTML`中回答我，我需要解析这个回答，通过一个错误处理程序传递它。

## [错误处理器](http://expressjs.com/en/guide/error-handling.html)

现在，我的应用程序有错误(在良好的意义上)，我需要一个错误处理程序。

### `src/index.js`

```
...
/**
 * Routes
 */
app.use('/v1', v1)

/**
 * ErrorHandler
 */
app.use((err, req, res, next) => {
  res.status(err.status || 500)
    .json({
      message: err.message,
      stack: err.stack
    })
})

module.exports = app 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**重要的是要记住只在开发中使用错误处理程序，在生产环境中尽量不要显示这么多信息。*

如果我再次询问我的服务器。

```
curl -i -H "Accept: text" localhost:3000

HTTP/1.1 406 Not Acceptable
X-Powered-By: Express
X-Content-Type-Options: nosniff
Content-Type: text/html; charset=utf-8
Content-Length: 1052
Date: Sun, 11 Dec 2016 18:42:12 GMT
Connection: keep-alive

{"message":"Not Acceptable","stack":"Error: Not Acceptable\n at app.use (/develop/another-todo-api/src/index.js:9:11)\n at Layer.handle [as handle_request] (/develop/another-todo-api/node_modules/express/lib/router/layer.js:95:5)\n at trim_prefix (/develop/another-todo-api/node_modules/express/lib/router/index.js:312:13)\n at /develop/another-todo-api/node_modules/express/lib/router/index.js:280:7\n at Function.process_params (/develop/another-todo-api/node_modules/express/lib/router/index.js:330:12)\n at next (/develop/another-todo-api/node_modules/express/lib/router/index.js:271:10)\n at expressInit (/develop/another-todo-api/node_modules/express/lib/middleware/init.js:33:5)\n at Layer.handle [as handle_request] (/develop/another-todo-api/node_modules/express/lib/router/layer.js:95:5)\n at trim_prefix (/develop/another-todo-api/node_modules/express/lib/router/index.js:312:13)\n at /develop/another-todo-api/node_modules/express/lib/router/index.js:280:7"} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的错误回复。

## 加贺

在构建我的 API 服务器时，我在代码中留下了一些未完成的事情，如果你不想继续阅读这些废话，可以跳过这一部分。

### 与[摩根一起伐木](https://www.npmjs.com/package/morgan)

express 有大量的中间件包，其中一个非常有用的是 Morgan，它是一个 HTTP 请求记录器，它会在终端中打印服务器将收到的所有请求。

```
npm i -S morgan 
```

Enter fullscreen mode Exit fullscreen mode

然后我需要*将*它附加到我的应用程序上。

#### `src/index.js`

```
const express = require('express')
const logger = require('morgan')
const app = express()
const v1 = require('./v1')

/**
 * Middlewares
 */
app.use(logger('dev'))

... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我运行我的服务器并向它发出一些请求:

```
npm start

> another-todo-api@0.0.0 start /develop/another-todo-api
> set DEBUG=another-todo:* && node bin/www

  another-todo:server Listening on port 3000 +0ms
GET / 404 5.469 ms - 13
GET /favicon.ico 404 0.905 ms - 24
GET /v1 200 2.275 ms - 25 
```

Enter fullscreen mode Exit fullscreen mode

### 林挺

我说过我在 javascript 代码中使用了[标准代码风格](http://standardjs.com/),但是我没有费心去确保每次有人在这个项目中写代码时都使用这种代码风格。最好的方法是用一些棉绒，为此我将使用 [ESLint](http://eslint.org/) 。

首先我需要安装我的开发依赖项(因为这个工具不会在生产中使用):

```
npm i -D eslint eslint-config-standard eslint-plugin-standard eslint-plugin-promise 
```

Enter fullscreen mode Exit fullscreen mode

***注:** `npm i -D`同`npm install --save-dev`。*

现在我需要在我的项目代码上定义一些[配置文件](http://eslint.org/docs/user-guide/configuring)。

#### `.eslintrc`

```
{  "extends":  "standard"  } 
```

Enter fullscreen mode Exit fullscreen mode

***注:**就那样！*

我将添加一个新的 npm 脚本。

#### `package.json`

```
...  "scripts":  {  "lint":  "eslint **/*.js",  "start":  "set DEBUG=another-todo:* && node bin/www",  "test":  "echo \"Error: no test specified\" && exit 1"  },  ... 
```

Enter fullscreen mode Exit fullscreen mode

是时候试试了。

```
npm run lint

> another-todo-api@0.0.0 lint /develop/another-todo-api
> eslint **/*.js 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**如果什么都没发生是因为没有错误，你可以通过在一些 JS 文件中添加一些`;`来重现错误。*

在文本编辑器中有几个插件可以让林挺代码*运行起来*，这样你就不需要运行林挺 npm 脚本了。在我的例子中，我将 [Atom](https://atom.io/) 与 [linter](https://atom.io/packages/linter) 和 [linter-eslint](https://atom.io/packages/linter-eslint) 包一起使用。

### [编辑配置](http://editorconfig.org/)

这是一个非常重要的工具，它避免了在 git 或 git diffs 上提交之间的许多干扰。这也有助于在项目中保持代码格式的一致性。

#### `.editorconfig`

```
# EditorConfig is awesome: http://EditorConfig.org 
root = true

[*]
charset = utf-8
end_of_line = lf
indent_size = 2
indent_style = space
insert_final_newline = true
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false 
```

Enter fullscreen mode Exit fullscreen mode

至于林挺也有插件可用于通常的文本编辑器。对于 Atom，有一个 [editorconfig](https://atom.io/packages/editorconfig) 包。

### [纱](https://yarnpkg.com/)

不久前，一个新的依赖管理器 Yarn 发布了，它与 npm 完全兼容。只需要安装然后就:

```
yarn 
```

Enter fullscreen mode Exit fullscreen mode

***注:**与`npm install`相同的是`yarn install`。您可以查看[纱线与 NPM 指令的对比](https://yarnpkg.com/en/docs/migrating-from-npm#toc-cli-commands-comparison)。*

将会出现一个名为`yarn.lock`的新文件，这是 Yarn 使用的信息，用于改进安装依赖关系的时间，如果你将文件的第一行标为红色，所有文件将会变得*清晰* :

```
# THIS IS AN AUTOGENERATED FILE. DO NOT EDIT THIS FILE DIRECTLY. 
```

Enter fullscreen mode Exit fullscreen mode

从这里，我可以开始使用纱线，而不是 NPM 的依赖和 NPM 脚本。

## 够了！！！

这篇文章太长了，会让你厌烦，所以我就说到这里。以后再说！

哦！是...你可以在[another-todo-API GitHub repo](https://github.com/AlbertoFdzM/another-todo-api/tree/post/01)上查看这段代码。