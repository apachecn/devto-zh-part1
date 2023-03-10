# 将 Create-React-App 与 Express 一起使用

> 原文：<https://dev.to/loujaybee/using-create-react-app-with-express>

虽然我自己设置了一个测试应用程序，但我找不到一种简单的方法在同一台服务器上部署 [Create React App](https://github.com/facebookincubator/create-react-app) 和 [Express](https://expressjs.com/) 。这需要一些调整，所以如果你想做同样的事情，这里有一些步骤。

**请注意**:这些步骤假设您想从同一个地方运行您的应用服务器和 API。如果您想简单地部署到像 heroku 这样的地方，这很有用。

如果你在之前没有使用过`create-react-app`，请阅读此文:如果你还没有使用过 create-react-app，它有两种服务模式:通过`npm run start`启动的热重装程序和优化的生产捆绑包，后者是标准的 index.html，你可以以任何你想要的方式提供服务。我希望有一种方法可以让`npm run start`方法和`npm run build`方法以同样的方式与我的 API 一起工作，一种方法是使用我将带您了解的代理设置。

#### 步骤 1:安装 create-react-app

```
 create-react-app your-app-name 
```

Enter fullscreen mode Exit fullscreen mode

#### 第二步:安装用于创建 react 应用程序的包

```
 npm install; 
```

Enter fullscreen mode Exit fullscreen mode

#### 第三步:安装 express

```
npm install express --save 
```

Enter fullscreen mode Exit fullscreen mode

#### 步骤 4:创建一个`server.js`文件

```
const express = require('express');
const bodyParser = require('body-parser')
const path = require('path');
const app = express();
app.use(express.static(path.join(__dirname, 'build')));

app.get('/ping', function (req, res) {
 return res.send('pong');
});

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});

app.listen(process.env.PORT || 8080); 
```

Enter fullscreen mode Exit fullscreen mode

#### 第五步:更新你的 package.json

将以下内容添加到 package.json 中

```
"proxy": "http://localhost:8080" 
```

Enter fullscreen mode Exit fullscreen mode

如果你不这样做，我们将不得不每次都创建缓慢的生产构建(而不是更快的开发`npm run start`方法)。这是因为`npm start`使用端口 3000，这不是运行 express APIs 的端口(8080)。

#### 第六步:启动快递服务器

```
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

或者`nodemon`如果你喜欢。

#### 第七步:启动你的 react 应用程序

保持节点运行，在单独的选项卡/窗口中执行此操作。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

使用热重装启动 react 构建。

# 结论

现在，您可以通过使用`npm run start`在`localhost:3000`上开发所有您想要的东西，并且您的 API 将按预期工作(尽管请求来自端口 3000)。

当您想要部署时，只需运行生产版本`npm run build`并从`localhost:8080`提供您的应用，在本例中是`node server.js`(注意 server.js 底部的端口号)。

利润。

* * *

Lou 是[云原生软件工程新闻简报](https://newsletter.thedevcoach.co.uk)的编辑，该新闻简报致力于使云软件工程更易于访问和理解，每两周您就会在收件箱中收到一份针对云原生软件工程师的最佳内容摘要。