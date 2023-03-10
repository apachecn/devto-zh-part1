# 将 Node.js REST API 迁移到无服务器

> 原文：<https://dev.to/adnanrahic/migrating-your-nodejs-rest-api-to-serverless-3gej>

我已经涉足了相当一部分无服务器的黑暗艺术。深入探讨没有专用服务器或您可以称之为自己的实例的各种利弊。即使严格来说他们不是。他们只是在某个漂浮在*云*中的秘密服务器群中。

许多用例让云提供商处理服务器管理、扩展和运行时间是有意义的。你是一个开发人员，为什么你需要用命令行来弄脏你的手。恶，终端！如何再次退出 Vim？**颤抖**

相信我，学习新事物并不容易。我绝不是一个高于平均水平的开发者。学习是困难的，即使你是一个开发者，并且习惯于学习新事物。将您的思维模式转变为使用无服务器架构是一项不小的壮举。以下是我对慢慢开始的看法。我将向您展示如何使用您已经熟悉的代码，并将其应用于无服务器环境。

如果你有一个应用程序在生产，你可以大幅削减成本。由于使用无服务器架构的自动伸缩特性，您可以放心，它将始终为访问您的 API 的所有用户提供服务。所以，如果你成功了，并在 Tech Crunch 上成为主角，用户的涌入不会破坏你所有的服务器，让你的用户无所适从。双关语。

### 从服务器到无服务器

我们的目标是利用现有的 Express API，对其稍加编辑，通过无服务器框架部署到 AWS。我希望你已经有了一个 AWS 帐户，并且已经在你的机器上安装了无服务器框架。如果没有，请检查[这个](https://dev.to/adnanrahic/a-crash-course-on-serverless-with-nodejs-5jp)并按照步骤设置无服务器框架的安装。另外，如果你更喜欢屏幕投射，[这里有](https://www.packtpub.com/web-development/serverless-javascript-example-video)一个课程，我通过视频来解释。

#### 让我们建立一个老式的服务器

我冒昧地用 Express REST API 创建了一个小的 repo。这是我以前的一篇文章中的一篇，你可能读过。我使用现有的 Express API 的目的是为了展示将它移植到无服务器是多么容易。

首先，让我们将回购克隆到我们的机器上。我们正在开发分支，我已经设置了所有必要的模块和配置。

```
$ git clone -b dev https://github.com/adnanrahic/nodejs-restful-api.git 
```

Enter fullscreen mode Exit fullscreen mode

这将把 repo 克隆到名为 nodejs-restful-api 的目录中。在您选择的代码编辑器中打开它。我们有些工作要做。

最重要的是。安装节点模块。

```
$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

运行 npm install 将安装来自 **package.json** 文件的所有模块。不会超过几秒钟。

完成后，我们需要配置数据库连接。我们将它保存在 **db.js** 文件中。打开它，你会看到**猫鼬**正在连接到一个数据库连接 URL，我们把它保存在一个环境变量中。

```
// db.js

var mongoose = require('mongoose');
mongoose.connect(process.env.DB, { useMongoClient: true }); 
```

Enter fullscreen mode Exit fullscreen mode

我们在一个**中设置这个环境变量。env** 文件。有一个示例文件，名为 **sample.variables.env** 。让我们打开它并将其重命名为 **variables.env** 。

```
// variables.env

DB=mongodb://localhost:27017/test 
```

Enter fullscreen mode Exit fullscreen mode

默认连接被设置为 MongoDB 的本地实例。您可以使用任何想要的连接 URL。 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 或者 [mLab](https://mlab.com/) 都可以。

***注意*** *:如果您想按照本教程中的代码进行操作，请创建一个 MongoDB Atlas 数据库集群。一旦我们将应用程序部署到 AWS，就会用到它。你可以跟随教程* [*这里的*](https://dev.to/adnanrahic/building-a-serverless-rest-api-with-nodejs-and-mongodb-43db) *来学习如何创建一个 Atlas 集群或者* [*这个*](https://hackernoon.com/restful-api-design-with-node-js-26ccf66eab09) *教程来创建一个 mLab 实例。*

剩下要做的就是运行服务器。跳回终点。

```
$ node server.js 
```

Enter fullscreen mode Exit fullscreen mode

如果您添加了一个有效的数据库连接 URL，它应该将 Express server 在端口 3000 上的监听记录回命令行。

使用失眠症，我将快速地向数据库添加一个新用户。

[![](img/981b34481fad9a5249623daf5b5d66ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LJP_iS4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Akyp_64_iPwvgAY33FIIpSA.png)

不要忘记选择**“表单 URL 编码”**作为内容类型。更改方法以获取和移除请求正文。现在检查用户添加是否正确。

[![](img/8d21ec9f09b080c8687c8b31aeabf04a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nIScPOTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASitbvbc4R5Lpoj-PKrfOsw.png)

似乎是对的。约翰活得好好的。

在服务器和运行的 Express API 上使用这种传统方法非常适合各种用例。但是，即使你没有任何真正的用户吞吐量，你也必须为此付费。但危险的是，如果你突然有大量用户涌入，你就必须手动调整规模。那一点也不好玩。无服务器自动为您做到这一点！

#### 迁移到无服务器

您猜怎么着，您可以使用上面的代码，通过无服务器框架将它部署到 AWS，只需做一些小的改动。实际上，您只是替换了 **server.js** 文件中的几行并安装了一个模块。最后，添加一个名为 **serverless.yml** 的无服务器配置文件。就是这样！

```
// server.js

// before

require('dotenv').config({ path: './variables.env' });
var app = require('./app');

var port = process.env.PORT || 3000;
var server = app.listen(port, function() {
  console.log('Express server listening on port ' + port);
});

// after

require('dotenv').config({ path: './variables.env' });
var app = require('./app');

var serverless = require('serverless-http');
module.exports.handler = serverless(app); 
```

Enter fullscreen mode Exit fullscreen mode

我们正在用[无服务器 http](https://github.com/dougmoscrop/serverless-http) 模块替换服务器。然后，这个模块被赋予整个 Express app 对象，并通过一个处理程序导出。我们将在 **serverless.yml** 文件中配置这个处理程序。但是首先，安装模块。

```
$ npm install --save serverless-http 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧。在项目目录的根目录下创建新的 **serverless.yml** 文件，并将这段代码粘贴到。保持缩进正确是非常重要的，因此我把它作为一个要点添加进来。