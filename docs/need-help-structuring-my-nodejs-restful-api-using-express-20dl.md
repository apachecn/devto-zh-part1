# 使用 Express 构建我的 Node.js RESTful API

> 原文：<https://dev.to/jjjjcccjjf/need-help-structuring-my-nodejs-restful-api-using-express-20dl>

大家好，简单介绍一下。几周前刚开始学 Node.js。我有 PHP 的背景，我已经专业地使用它 2-3 年了。

我知道 Express.js 在构建您的应用程序方面提供了自由，但是我需要一些建议，告诉我如何构建他们的应用程序(RESTful API ),假设它在未来可以扩展。

我当前的目录结构如下:

```
- config
- controllers
- models
- routes
  - api
- app.js 
```

Enter fullscreen mode Exit fullscreen mode

我主要是把这个回购作为我的灵感。然而，[weave hive/project-guidelines](https://github.com/wearehive/project-guidelines#6-structure-and-naming)表示这种结构不好。我想重构我的代码，以便能够遵循它，因为就我个人而言，如果我的项目是可伸缩的，这将更有意义；我的项目将由*特征*而不是*角色*来划分。但是你是如何将这些特征分组的呢？

我用 Express.js 是因为它是最受欢迎的一个，而且好像每个人都知道它或者首先研究它。我对其他框架持开放态度，但对于像我这样的初学者来说，使用 Restify 或任何其他框架比从 Express.js 开始更实际吗？

此外，这里是我的依赖关系，所以你可以有一个我正在使用的堆栈的想法。

```
 "dependencies": {
    "bcrypt-nodejs": "0.0.3",
    "dotenv": "^4.0.0",
    "express": "^4.16.2",
    "jsonwebtoken": "^8.1.0",
    "mongoose": "^4.13.7",
    "mongoose-unique-validator": "^1.0.6",
    "nodemailer": "^4.4.1",
    "passport": "^0.4.0",
    "passport-jwt": "^3.0.1"
  } 
```

Enter fullscreen mode Exit fullscreen mode

附注:对于如何构建版本化的应用程序，有什么建议吗？

你通常如何构造一个 RESTful API？