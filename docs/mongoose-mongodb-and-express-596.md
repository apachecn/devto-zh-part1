# Mongoose、MongoDB 和 Express

> 原文：<https://dev.to/albertofdzm/mongoose-mongodb-and-express-596>

ExpressJS API 开发的第一步是连接到 MongoDB 数据库，并开始使用端点管理数据。

***注:**这是关于**用 Express** 构建 API 系列文章的第 3 篇。基于我上一篇关于用 Express 路由 [API 的文章，我将继续开发](https://dev.to/2016/12/17/api-routing-with-express/)[生成的代码](https://github.com/AlbertoFdzM/another-todo-api/tree/post/02)。*

上次，令人敬畏的 TODO API 留下了一个很好的 API 路由层次结构，但是！而这是一个重要的“但是”。我没有为将来的使用存储任何类型的数据，它只是在临时内存中存储 TODOs，所以一旦服务器停止，所有的信息都将丢失。

*本帖最初发表于[onlythepixel.com](http://onlythepixel.com/2017/01/05/mongoose-mongodb-and-express/)T3】*

## 要求

对于这篇文章，我需要在我的机器上安装 [MongoDB](https://www.mongodb.com/download-center#community) 以便能够在我的本地环境中开发具有真实连接的 API。

***注意:**我需要注意[让我的 MongoDB 启动并运行](https://docs.mongodb.com/manual/administration/install-community/)以便能够使用它。*

此外，我将需要[mongose](https://www.npmjs.com/package/mongoose)作为我的项目的依赖，这个包将帮助我与数据库通信和数据模型:

```
yarn add mongoose 
```

Enter fullscreen mode Exit fullscreen mode

## mongose 连接到 MongoDB

首先，我需要让 mongoose 连接到我的本地 MongoDB，所以我将创建一个新的脚本来完成这项工作。

### `src/db.js`

```
const mongoose = require('mongoose')
const debug = require('debug')
const log = debug('another-todo:database')
const error = debug('another-todo:database:error')

// First I define my DB URI or
// make my script take it from the env variables
const DB_URI = process.env.DB_URI || 'mongodb://localhost/another-todo'

// Define some basic methods to
// connect/disconnect to the DB
const db = {

  connect () {
    return mongoose.connect(DB_URI)
  },

  disconnect () {
    return mongoose.connection.close(() => {
      process.exit(0)
    })
  }
}

// This let mongoose use the node's default promises
mongoose.Promise = global.Promise

// Logs for our app
mongoose.connection.on('connected', () => {
  log('Mongoose connection open to ' + DB_URI)
})

// More logs...
mongoose.connection.on('disconnected', () => {
  log('Mongoose disconnected')
})

// Logs that I hope to not see
mongoose.connection.on('error', (err) => {
  error(err)
})

// Handle process terminations
// this ensures that there is any connection
// open with DB when I stop the app
process
  .on('SIGINT', db.disconnect)
  .on('SIGTERM', db.disconnect)

// finally I only expose the methods to being used by my app script
module.exports = db 
```

Enter fullscreen mode Exit fullscreen mode

现在我只需要在我的应用程序上使用我的`db`脚本。

### `src/index.js`

```
const express = require('express')
const logger = require('morgan')
const bodyParser = require('body-parser')
const app = express()
const v1 = require('./v1')
const db = require('./db')

// Connect to DB!!
db.connect()

// Middlewares
... 
```

Enter fullscreen mode Exit fullscreen mode

## 猫鼬模型

现在是时候定义第一个 mongoose 模型了，此时唯一的模型或者存储在 DB 中的**相关数据是我的任务，所以我只需要这个模型。**

我将使用我在上一篇文章中使用的相同的 [**数据结构**。](https://dev.to/2016/12/17/api-routing-with-express/)

### `src/models/task.js`

```
const mongoose = require('mongoose')
const Schema = mongoose.Schema

// I'm going to define a new schema
// Here is where I define the properties
// that my data is going to have
// along with its validations 
const taskSchema = new Schema({

  // A property 'description' of type string
  // with a default to a empty string
  description: {
    type: String,
    default: ''
  },

  // And a boolean property with false as default
  isDone: {
    type: Boolean,
    default: false,
    required: true
  }

}, {timestamps: true})

module.exports = mongoose.model('Task', taskSchema) 
```

Enter fullscreen mode Exit fullscreen mode

***注意:**`timestamps`让我不必定义属性`createdAt`或`updatedAt`，因为一旦属性被设置为`true`，它就会添加这个值。*

现在是时候在我的 API 中使用这个模型了。

## `src\v1\tasks.js`

```
const router = require('express').Router()
const Task = require('../models/task')

router.route('/')

  .get((req, res, next) => {
    // I exec the find without conditions 
    // to retrieve all my tasks
    Task.find((err, tasks) => {
      if (err) return next(err)

      return res.json(tasks)
    })
  })

  .post((req, res, next) => {
    Task.create(req.body, (err, task) => {
      if (err) return next(err)

      return res.status(201).json(task)
    })
  })

  .delete((req, res, next) => {
    // This method is similar to find but instead
    // it removes all the occurrences 
    Task.remove((err) => {
      if (err) return next(err)

      return res.status(204).end()
    })

    res.status(204).end()
  })

router.param('taskId', (req, res, next, id) => {
  // Handle to find the requested resouce
  Task.findById(id, (err, task) => {
    if (err) return next(err)

    // If the task is not found then the app returns a 404
    if (!task) {
      err = new Error('Task not found')
      err.status = 404
    } else {
      req.task = task
    }

    return next(err)
  })
})

router.route('/:taskId')

  .get((req, res, next) => {
    return res.json(req.task)
  })

  .put((req, res, next) => {
    // I'm not using req.task.update() because
    // that method doesn't return the task on the callback
    Task.findByIdAndUpdate(req.task.id, {
      $set: req.body
    }, {
      // Returns the updated task
      new: true,
      // Set the whole document even if we are not
      // receiving all the properties
      overwrite: true,
      // Run validations if we have them
      runValidators: true
    }, (err, task) => {
      if (err) return next(err)

      return res.json(task)
    })
  })

  .patch((req, res, next) => {
    Task.findByIdAndUpdate(req.task.id, {
      $set: req.body
    }, {
      new: true,
      runValidators: true
    }, (err, task) => {
      if (err) return next(err)

      return res.json(task)
    })
  })

  .delete((req, res, next) => {
    Task.findByIdAndRemove(req.task.id, (err) => {
      if (err) return next(err)

      res.status(204).end()
    })
  })

module.exports = router 
```

Enter fullscreen mode Exit fullscreen mode

***注:**你可以查看[mongose API 文档](http://mongoosejs.com/docs/api.html)了解它不同方法的信息。*

现在是时候试试了！

## 卷曲

```
$ curl -X GET "http://localhost:3000/v1/tasks"

[]

$ curl -X POST "http://localhost:3000/v1/tasks" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'description=test'

{
    "__v": 0,
    "updatedAt": "2017-01-05T17:53:37.066Z",
    "createdAt": "2017-01-05T17:53:37.066Z",
    "_id": "586e88217106b038d820a54e",
    "isDone": false,
    "description": "test"
}

$ curl -X POST "http://localhost:3000/v1/tasks" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'description=test'

{
    "__v": 0,
    "updatedAt": "2017-01-05T17:53:55.067Z",
    "createdAt": "2017-01-05T17:53:55.067Z",
    "_id": "586e88337106b038d820a54f",
    "isDone": false,
    "description": "test"
}
$ curl -X GET "http://localhost:3000/v1/tasks"

[
    {
        "__v": 0,
        "updatedAt": "2017-01-05T17:53:37.066Z",
        "createdAt": "2017-01-05T17:53:37.066Z",
        "_id": "586e88217106b038d820a54e",
        "isDone": false,
        "description": "test"
    },
    {
        "__v": 0,
        "updatedAt": "2017-01-05T17:53:55.067Z",
        "createdAt": "2017-01-05T17:53:55.067Z",
        "_id": "586e88337106b038d820a54f",
        "isDone": false,
        "description": "test"
    }
]

$ curl -X DELETE -i "http://localhost:3000/v1/tasks"

HTTP/1.1 204 No Content
X-Powered-By: Express
Date: Thu, 05 Jan 2017 17:54:47 GMT
Connection: keep-alive

$ curl -X POST "http://localhost:3000/v1/tasks" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'description=test'

{
    "__v": 0,
    "updatedAt": "2017-01-05T17:54:53.555Z",
    "createdAt": "2017-01-05T17:54:53.555Z",
    "_id": "586e886d7106b038d820a550",
    "isDone": false,
    "description": "test"
}

$ curl -X GET "http://localhost:3000/v1/tasks/586e886d7106b038d820a550"

{
    "_id": "586e886d7106b038d820a550",
    "updatedAt": "2017-01-05T17:54:53.555Z",
    "createdAt": "2017-01-05T17:54:53.555Z",
    "__v": 0,
    "isDone": false,
    "description": "test"
}

$ curl -X PATCH "http://localhost:3000/v1/tasks/586e886d7106b038d820a550" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'description=amazing' 

{
    "_id": "586e886d7106b038d820a550",
    "updatedAt": "2017-01-05T17:56:06.879Z",
    "createdAt": "2017-01-05T17:54:53.555Z",
    "__v": 0,
    "isDone": false,
    "description": "amazing"
}

$ curl -X PATCH "http://localhost:3000/v1/tasks/586e886d7106b038d820a550" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'isDone=true' 

{
    "_id": "586e886d7106b038d820a550",
    "updatedAt": "2017-01-05T17:56:24.328Z",
    "createdAt": "2017-01-05T17:54:53.555Z",
    "__v": 0,
    "isDone": true,
    "description": "amazing"
}

$ curl -X PUT "http://localhost:3000/v1/tasks/586e886d7106b038d820a550" \
> -H "Content-Type: application/x-www-form-urlencoded" \
> -d 'isDone=false' 

{
    "_id": "586e886d7106b038d820a550",
    "createdAt": "2017-01-05T17:56:40.478Z",
    "updatedAt": "2017-01-05T17:56:40.478Z",
    "isDone": false,
    "description": ""
}

$ curl -X DELETE -i "http://localhost:3000/v1/tasks/586e886d7106b038d820a550"

HTTP/1.1 204 No Content
X-Powered-By: Express
Date: Thu, 05 Jan 2017 17:57:35 GMT
Connection: keep-alive

$ curl -X GET "http://localhost:3000/v1/tasks"

[] 
```

Enter fullscreen mode Exit fullscreen mode

如果服务器停止并重新启动，任务仍然存在，任务完成！

***注:**我推荐用[邮差](https://www.getpostman.com/)代替 cURL 测试 API。*

这就是我所想的。你可以[在 GitHub](https://github.com/AlbertoFdzM/another-todo-api/tree/post/03) 上查看代码。