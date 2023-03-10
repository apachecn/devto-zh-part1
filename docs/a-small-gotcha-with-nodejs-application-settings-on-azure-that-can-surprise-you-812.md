# Azure 上 NodeJS 应用程序设置的一个小陷阱可能会让你大吃一惊

> 原文：<https://dev.to/codeprototype/a-small-gotcha-with-nodejs-application-settings-on-azure-that-can-surprise-you-812>

*也贴在[介质上](https://medium.com/@kevinle/a-small-gotcha-with-nodejs-application-settings-on-azure-that-can-surprise-you-2e041cf783ec)T3】*

以下代码在 Azure 应用服务之外托管的 NodeJS 服务器上运行得非常好:

```
const apiAiApp = require("apiai")(Constants.API_AI_ACCESS_TOKEN);

module.exports = function(app, server) {
   //Do something with apiAiApp
} 
```

在开发过程中，在本地服务器上运行 NodeJS，在 package.json 文件中设置环境变量 API_AI_ACCESS_TOKEN。为了完整起见，让我展示 constants.js 文件，因为它只有几行:

```
const env = {
  API_AI_ACCESS_TOKEN: process.env.API_AI_ACCESS_TOKEN,
} 
```

在 Azure 上，API_AI_ACCESS_TOKEN 等环境变量必须在应用设置刀片中设置。这就是 UI 的做法。我们也可以使用命令行来设置它。无论哪种方式，都必须先设置环境变量。但是即使这样，当我们试图访问网络应用程序时，它还是给出了一个类似于
的错误页面

```
iisnode encountered an error when processing the request.

 HRESULT: 0x6d
 HTTP status: 500
 HTTP subStatus: 1013
 HTTP reason: Internal Server Error 
```

IIS？那是什么？如果这是你第一次尝试在 Azure 上部署你的应用，你可能不会感到如此“有压力”。但在我的情况下，我的应用程序在生产中工作得很好。突然间，Azure 上什么都不用了，也没有控制台可以看，就像我的本地服务器一样。这里有一个很好的资源可以帮我:

[https://blogs . msdn . Microsoft . com/azureossds/2015/08/19/debug-node-js-we B- apps-on-azure/](https://blogs.msdn.microsoft.com/azureossds/2015/08/19/debug-node-js-web-apps-on-azure/)

长话短说，修复非常简单。简单地将代码重组如下:

```
const apiAi = require("apiai");

module.exports = function(app, server) {
   const apiAiApp = apiAi(Constants.API_AI_ACCESS_TOKEN);

   //Now, do something with apiAiApp
} 
```