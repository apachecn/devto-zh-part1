# 使用 Express 的 API 路由

> 原文：<https://dev.to/albertofdzm/api-routing-with-express>

***注:**这是关于**用 Express** 构建 API 系列文章的第二篇。*

基于我上一篇关于用 Node 和 Express 制作 API 的文章，我将继续开发由 T2 生成的代码。

所以，[我离开地下室去准备我的 TODO API](https://dev.to/2016/12/11/making-apis-with-node-and-express/)。现在是时候使用这个 API 将要使用的不同端点和 HTTP 动词/方法了。

*本帖最初发表于[onlythepixel.com](http://onlythepixel.com/2016/12/17/api-routing-with-express/)T3】*

## `/v1/tasks`

我将开始用**任务集合**构建我的 API 端点。

### `[GET] /v1/tasks`

我需要做的第一件事是`GET`我的创新 TODO API 中的任务列表，我认为构建它的最佳方式是创建一个**新的隔离快速路由器实例**。

#### `src/v1/tasks.js`

```
const router = require('express').Router()

/**
 * TODO: Store data in DB.
 */
let tasks = [
  {
    description: 'Another task',
    isDone: false,
    createdAt: Date.now()
  }
]

router.route('/')

  .get((req, res, next) => {
    return res.json(tasks)
  })

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

很简单！现在我需要在我的 API 上**安装**那个路由器，我将删除旧的`Let's TODO!`消息:

#### `src/v1/index.js`

```
const router = require('express').Router()
const tasks = require('./tasks')

router.use('/tasks', tasks)

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

让我们试试新的端点(现在我已经实现了`yarn`，我可以用``yarn start` instead of `npm start`):` `bash
curl -X GET localhost:3000/v1/tasks
[
{
"createdAt" : 1481985039988,
"isDone" : false,
"description" : "Another task"
}
]` ``
***运行服务器了。注意:**如果在服务器运行时代码发生了变化，需要重启服务器来查看变化。*

现在，我将继续创建一个新任务

### `[POST] /v1/tasks`

它应该与`GET`方法在同一个路由器实例中。这一次我需要一个新的中间件来正确解析[请求体](http://expressjs.com/en/4x/api.html#req.body)、[体解析器](https://www.npmjs.com/package/body-parser)(这一次我将用 Yarn 安装它)。

``bash
yarn add body-parser
``
***注:**同执行`npm i -S body-parser`*

现在它需要附加到应用程序上

#### `src/index.js`

``JavaScript
const express = require(' express ')
const logger = require(' mor gan ')
const body parser = require(' body-parser ')
const app = express()
const v1 = require('。/v1’)`

 `/**

*   middleware */app . use(logger(' dev '))app . use(body parser . JSON())app . use(body parser . urlencoded({ extended:true }))

...
 ``_**Note:** It will inject data from the request into`请求体` . _`

 `现在是请求处理程序。

#### `src/v1/tasks.js`

` `javascript
...`

 `router.route('/')

。get((req，res，next)= > {
return RES . JSON(tasks)
})

。post((req，res，next)= > {
const new task = req . body

```
newTask.createdAt = Date.now()
newTask.isDone = false
tasks.push(newTask)

return res.status(201).json(newTask) 
```

Enter fullscreen mode Exit fullscreen mode

})

module . exports = router
``
***注:**利用 [HTTP 状态码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。*`

 `完成后，让我们用一个`POST`请求再试一次:
``bash
curl-X POST-H " Content-Type:application/JSON "-data ' { " description ":"也是另一个任务更多" } ' localhost:3000/v1/tasks
{ " description ":"也是另一个任务更多"，" createdAt":1481986821539}`

 `curl-X GET localhost:3000/v1/tasks
[
{
【createdAt】:1481986807819，
【isDone】:false，
【描述】:另一个任务
}，
{
【createdAt】:1481986821539，
【isDone】:false，
【描述】:还另一个任务更
}【说明】。

该删除了！由于这是一个指向集合的端点，当它接收到一个`DELETE`请求**时，它应该删除集合**的所有项目。

### `[DELETE] /v1/tasks`

#### `src/v1/tasks.js`

` `javascript
...`

 `。post((req，res，next)= > {
const new task = req . body

```
newTask.createdAt = Date.now()
tasks.push(newTask)

return res.json(newTask) 
```

Enter fullscreen mode Exit fullscreen mode

})

。delete((req，res，next) => {
tasks = []

```
res.status(204).end() 
```

Enter fullscreen mode Exit fullscreen mode

})

module.exports =路由器
T1】

删除所有的东西！

``bash
curl-X DELETE-I localhost:3000/v1/tasks`

 `HTTP/1.1 204 无内容
X-Powered-By:Express
ETag:W/" 2-11 fxoyiyfpmxmany 4kgjzg "
日期:2016 年 1 2 月 17 日星期六 17:13:07 GMT
连接:keep-alive
`'`

 `我已经完成了这个端点。

## `/v1/tasks/:taskId`

现在是处理单一任务端点的时候了。在这里，我将利用一个 [Express 特性来解析 URL，在本例中，将 URL 的一个片段作为一个参数](http://expressjs.com/en/4x/api.html#app.param)，并将其命名为`taskId`。

### `taskId` param

我将为 tasks router 定义一个新的参数，以通过 URL 中的任务 ID 来获取某个任务。

#### `src/v1/tasks.js`

` `javascript
...`

 `router.route('/')

...

router.param('taskId '，(req，res，next，id)= > {
const task = tasks[id]
let err

如果(！task) {
err =新错误('未找到任务')
err . status = 404
} else {
req . Task = Task
}

return next(err)
})

module.exports =路由器
T1】

### `[GET] /v1/tasks/:taskId`

然后，我只需要在一个新的端点中用找到的任务进行回复，监听斜线之后的任何请求，无论这些请求是通过`:taskId`指向`/v1/tasks/:taskId`的(即:`/v1/tasks/my-task-id`还是`/v1/tasks/01234`)。

#### `src/v1/tasks.js`

` `javascript
...`

 `router.param('taskId '，(req，res，next，id) => {
...
})

router.route('/:taskId ')

。get((req，res，next)= > {
return RES . JSON(req . task)
})

module.exports =路由器
T1】

让我们试试吧

``bash
curl-X GET localhost:3000/v1/tasks/0
{
"描述":"另一个任务"，
"isDone" : false，
" createdAt ":1481996187751
}`

 `curl-X GET-I localhost:3000/v1/tasks/1234

HTTP/1.1 404 未找到
X-Powered-By:Express
Content-Type:application/JSON；charset = utf-8
Content-Length:1082
ETag:W/" 43a-4d 6 NK 29 ikr v0 b 3 jsadqgva "
日期:2016 年 12 月 17 日星期六 17:46:36 GMT
连接:keep-alive
{
"stack ":"错误:在 router . param(/develop/another-todo-API/src/v1/tasks . js:38:1

### `[POST] /v1/tasks/:taskId`

这里需要注意的是，根据定义，对 API REST 中指定资源的`POST`请求应该**完全覆盖资源**， 也就是说，如果我对这个数据做了一个`GET`到`/v1/tasks/0`的接收:
``json
{
"description" : "Another task",
"isDone" : false,
"createdAt" : 1481996187751
}
``
如果我对这个数据做了一个`POST`到`/v1/tasks/0`的接收:
``json
{ "isDone": true }
``
下一次我做一个`GET`到`/v1/tasks/0`的接收，我会收到下一个响应:
``json
{ "isDone": true }
``
**客户端对 API 资源进行`POST`的正确方式是在请求中提供所有的资源信息，以避免信息丢失**。

现在回去写代码吧！

#### `src/v1/tasks.js`

` `javascript
...
。post((req，res，next) = > {
常量更新任务= req.body`

```
tasks[req.params.taskId] = updatedTask

return res.json(updatedTask) 
```

Enter fullscreen mode Exit fullscreen mode

})
...
```

 `### `[PATCH] /v1/tasks/:taskId`

现在 **`PATCH`请求是用于部分更新 API REST** 中的资源的请求。

#### `src/v1/tasks.js`

` `javascript
...
。patch((req，res，next)=>{
for(let prop in req . body){
tasks[req . params . taskid][prop]= req . body[prop]
}`

```
return res.json(tasks[req.params.taskId]) 
```

Enter fullscreen mode Exit fullscreen mode

})
...
```

 `让我们用`curl` :
``bash
curl -X PATCH -H "Content-Type: application/json" --data '{"isDone": true}' localhost:3000/v1/tasks/0
{
"isDone" : true,
"description" : "Another task",
"createdAt" : 1481998868351
}
``试试这个

哦耶！

### `[DELETE] /v1/tasks/:taskId`

我认为这里没有太多要解释的，与整个集合的`DELETE`非常相似，只是这个集合只删除了一个资源。

#### `src/v1/tasks.js`

` `javascript
...
。删除((req，res，next)=>{
tasks . splice(req . params . taskid，1)`

```
res.status(204).end() 
```

Enter fullscreen mode Exit fullscreen mode

})
...
```

 `## 路线的终点

嗯，我认为有了这个令人敬畏的另一个 TODO API 就完全正常了，也许我应该关心的是**信息没有被存储在任何地方，每次服务器停止时，我都会丢失所有信息**，但那将是另一次！

评论和[查看 GitHub 上的代码](https://github.com/AlbertoFdzM/another-todo-api/tree/post/02)！`````````````````