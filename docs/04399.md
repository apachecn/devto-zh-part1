# Node.js + Express 服务器设置

> 原文：<https://dev.to/ryhenness/nodejs--express-server-setup-6ch>

嘿嘿，很高兴你来了！我们将讨论如何使用 Express web 应用程序框架在本地设置 Node.js 服务器。如果你对 Node.js 了解不多，只需要了解这个:Node.js 是一个开源的、跨平台的 JavaScript 运行时环境，用于执行 JavaScript 代码的服务器端。我希望这篇教程能帮助你理解这到底意味着什么。
你可以在这里找到视频教程[，在](https://www.youtube.com/watch?v=HkK5lGx9DRU&t=7s) [GitHub](https://github.com/henness17/SweetCode/tree/master/001-Node-Server-Setup-with-Express) 上找到代码。

* * *

# 先做最重要的事

如果你还没有这样做，你需要安装 Node.js。这可以在[https://nodejs.org/](https://nodejs.org/)完成。您应该在首页看到几个下载链接，使用 LTS 版本:

[![Download box](img/8f71eb52ecb3c638d5b2a0e2fae6a96d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UAnhkY5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i64.tinypic.com/25au1ky.png)

设置很简单，只需点击下一步，下一步，下一步，直到它全部完成。你能行的。:)

* * *

# Server.js 设置

### 添加快递

去打开一个文本编辑器，我用的是[崇高文本](https://www.sublimetext.com/)(免费的！)并创建一个新文件。让我们称这个文件为`server.js`(这将是我们使用的唯一文件)。现在，我们想在这个`server.js`文件中做的第一件事是向它添加 *Express* 。Express 是一个快速、非个性化、极简主义的 web 框架。我们可以通过添加这两行代码来实现:

```
 var express = require('express');
    var app = express(); 
```

### 添加路线

接下来我们要做的是将一条*路线*添加到这个`server.js`文件中。路由指的是应用程序(服务器)端点的定义，以及它们如何响应客户端(web 浏览器)的请求。我们可以通过添加这段代码来实现:

```
 // Our first route
    app.get('/', function (req, res) {
        res.send('Hello Dev!');
    }); 
```

### 监听端口

所以现在我们有了一个应用程序框架和一个路由，我们需要做的最后一件事就是告诉我们的服务器要监听哪个*端口*。我们可以通过添加以下代码来实现:

```
 // Listen to port 5000
    app.listen(5000, function () {
        console.log('Dev app listening on port 5000!');
    }); 
```

### 最终产品

好吧，酷！我们现在已经建立了整个服务器文件。它的内容看起来像这样:

```
 var express = require('express');
    var app = express();

    // Our first route
    app.get('/', function (req, res) {
        res.send('Hello Dev!');
    });

    // Listen to port 5000
    app.listen(5000, function () {
        console.log('Dev app listening on port 5000!');
    }); 
```

* * *

# 运行 Server.js

### 导航至 Server.js

我们现在有了一个服务器文件，但是如何让它运行起来呢？我们首先需要打开 *Node.js 命令提示符*，你可以通过使用你机器的搜索功能找到它。现在，我们需要使用以下命令导航到服务器文件的目录:

```
cd [directory name] // move into a directory
cd .. // move backward a directory 
```

你可以在这里找到更多关于命令行导航的信息。

### 定义 NPM 包

我们可以单独运行服务器文件吗？嗯，没有。但幸运的是，我们可以使用 *NPM* 为我们设置其他一切。NPM 代表“节点包管理器”。我们要做的第一件事是运行命令:

```
npm init 
```

这将提示输入一些信息并生成一个`package.json`文件。这个包文件定义了我们的应用程序是什么。一旦完成了软件包文件的设置，就可以继续安装 Express 了。

### 安装快递

我们知道我们的应用程序想要使用 Express 框架，并且我们为它添加了代码——但是我们仍然需要通过 NPM 将它添加到我们的包中。要添加 Express，请运行以下命令:

```
npm install --save express 
```

这告诉我们的 NPM 安装 Express 包，并将其保存到我们项目的包依赖项中。如果您打开包文件，您将在依赖项部分看到 Express。

### 运行服务器

现在我们在这里！我们终于可以运行服务器了。在我们的服务器目录中，运行以下命令:

```
node server.js 
```

这告诉 Node.js 启动服务器！你会看到“开发应用程序监听端口 5000！”。打开你的网络浏览器，进入 [http://localhost:5000](http://localhost:5000) 。你应该看到“你好，戴夫！”这是我们对第一条路线的回应内容。您可以在命令提示符下使用`Ctrl+C`来停止服务器运行。

* * *

# 添加另一条路线

好吧，酷。我们的服务器正在运行，我们的索引得到了响应。但是，如果我们想添加另一条路线，我们该怎么做呢？我们可以通过添加另一个代码块来添加另一个路由:

```
 // Our second route
    app.get('/dev', function (req, res) {
        res.send('Hello, you are now on the Dev route!');
    }); 
```

现在，通过在命令提示符下运行命令`node server.js`再次启动您的服务器。导航到[http://localhost:5000/dev](http://localhost:5000/dev)，您将看到与从索引路由得到的不同的响应——这是来自`/dev`路由的响应。酷！

* * *

# 回顾

现在，您已经成功地设置了一个具有多个本地运行的路由的最小服务器。您可以通过创建 Node.js 应用程序、添加 Express package 依赖项和实现路由来实现这一点。恭喜你！可以通过[Auto-Deploy a node . js Server:Heroku+GitHub](https://dev.to/ryhenness/auto-deploy-a-nodejs-server-heroku--github-em)了解更多关于实时部署的内容。如果你想了解更多关于路由的信息，请查看我的文章[和 Node.js](https://dev.to/ryhenness/external-routes-with-nodejs-1ni) 的外部路由。

* * *

[https://www.youtube.com/embed/HkK5lGx9DRU](https://www.youtube.com/embed/HkK5lGx9DRU)