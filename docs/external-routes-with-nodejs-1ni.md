# 使用 Node.js 的外部路由

> 原文：<https://dev.to/ryhenness/external-routes-with-nodejs-1ni>

怎么了，很高兴你来了！我们将讨论如何使用 [Express](https://expressjs.com/) 路由器为 Node.js 服务器设置和构建外部路由。外部路径可以很好地保持代码的组织性、可维护性和易理解性；它们有助于创建一个易于检查的结构，以便找到与您的作品相关的特定作品。如果你以前没有设置过 Node.js 服务器，一定要让[看看这个教程](https://dev.to/ryhenness/nodejs--express-server-setup-6ch)，它详细说明了如何用 Express 设置 Node.js 服务器。
你可以在这里找到视频教程[，还有](https://www.youtube.com/watch?v=ctUH5Hj6WLM&t=11s) [GitHub](https://github.com/henness17/SweetCode/tree/master/002-Node-External-Routes) 上的代码。

* * *

# 什么意思？

好吧，那么我说的外部路线是什么意思？好吧，当你在你的服务器里设置了越来越多的路由时，事情会变得有点笨拙和混乱。外部路由是一种组织代码的方式，通过将路由实现从主服务器文件中取出，并将其移动到一个单独的路由器文件中，使代码保持良好的组织状态。

* * *

# 能举个例子吗？

当然了。假设我们有一个简单的服务器文件，其中有许多路由，`index.js`:

```
 var express = require('express');
    var app = express();

    // Our first route
    app.get('/', function (req, res) {
        res.send('Hello Index!');
    });

    // Our A route
    app.get('/A', function (req, res) {
        res.send('Hello A!');
    });

    // Our B route
    app.get('/B', function (req, res) {
        res.send('Hello B!');
    });

    // Our C route
    app.get('/C', function (req, res) {
        res.send('Hello C!');
    });

    // Listen to port 5000
    app.listen(5000, function () {
      console.log('Dev app listening on port 5000!');
    }); 
```

我们可以看到，添加更多的路由和功能将很容易使这个文件变得非常混乱和难以维护。它现在可能做不了太多，但是想象一下用它开发几个星期...月份...几年。我们可以开始添加处理社交媒体验证、API，甚至只是静态页面的路由。因此，我们希望开始分离和结构化我们的代码，因为我们的路由可以变得更加合适，并且在索引文件开始获得更多功能之前，它可能需要在未来处理不仅仅是路由(如果这是一个可能的应用程序，它就会这样)。

* * *

# 目标

我们的目标是保持索引的整洁和代码的有序。这是我们的文件/文件夹结构的概述，以及我们希望如何从主索引文件中分离出这些路由定义。所以继续创建这个结构，创建一个名为`index.js`的文件和一个名为`routes`的文件夹，其中包含一个名为`externalRoutes.js`的文件:

```
.
├── index.js              
└── routes             
   └── externalRoutes.js 
```

* * *

# 定义路由器

现在该清理我们的索引文件了(这个文件用在了*我能举个例子吗？*一节)。让我们用路由器定义替换所有这些路由。这个路由器定义将告诉索引文件在哪里寻找要定义的路由。在这种情况下，我们告诉索引(位于顶层目录)在名为`routes`的文件夹中搜索名为`externalRoutes.js`的文件，该文件包含外部路由实现(嗯，还没有...但是会的)。在`index.js`里面，我们来补充一下:

```
// Our external route
var externalRoutes = require('./routes/externalRoutes');
app.use('/externalRoutes', externalRoutes); 
```

整个`index.js`文件将如下所示:

```
// index.js
var express = require('express');
var app = express();

// Our external route
var externalRoutes = require('./routes/externalRoutes');
app.use('/externalRoutes', externalRoutes);

// Listen to port 5000
app.listen(5000, function () {
  console.log('Dev app listening on port 5000!');
}); 
```

现在已经干净多了，不是吗？:)

* * *

# 实现路由器

但是那些路线去了哪里？！放心吧！我们将把它们存储在我们的`externalRoutes.js`中。我们将创建一个模块导出，它将导出 externalRoutes 变量的定义。首先，在`externalRoutes.js`中创建一个`module.exports`函数，如下所示:

```
module.exports = (function() {
    'use strict';
})(); 
```

现在将变量添加到我们想要导出的`module.exports`体中，姑且称之为`externalRoutes`:

```
module.exports = (function() {
    'use strict';

    var externalRoutes = require('express').Router();

    return externalRoutes;
})(); 
```

最后，将我们一直在等待的路线添加到`module.exports`中，您就有了一些外部路线！`externalRoutes.js`的整个身体看起来是这样的:

```
module.exports = (function() {
    'use strict';
    var externalRoutes = require('express').Router();

    externalRoutes.get('/', function (req, res) {
        res.send('Hello ExternalRoutes!');
    });
    externalRoutes.get('/someRoute', function (req, res) {
        res.send('Hello SomeRoute!');
    });

    return externalRoutes;
})(); 
```

* * *

# 浏览器上的外部路线

现在我们已经设置好了您的外部路由，启动服务器和 web 浏览器(对于那些不知道如何操作的人，请从顶层目录运行命令`node index.js`)。前往地址[http://localhost:5000/external routes](http://localhost:5000/externalRoutes)和[http://localhost:5000/external routes/some route](http://localhost:5000/externalRoutes/someRoute)查看您的路线及其响应！

* * *

# 回顾

恭喜你！现在，您已经成功地在 Node.js 服务器上设置了外部路由。您可以通过从索引文件中删除路由并以允许路由成为外部文件一部分的方式构建您的架构来实现这一点。当您开始构建服务器并添加其功能时，这使得您的代码更易于维护。如果您想了解部署，请查看[Auto-Deploy a node . js Server:Heroku+GitHub](https://dev.to/ryhenness/auto-deploy-a-nodejs-server-heroku--github-em)。

* * *

[https://www.youtube.com/embed/ctUH5Hj6WLM](https://www.youtube.com/embed/ctUH5Hj6WLM)