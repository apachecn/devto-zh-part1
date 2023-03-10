# 使用 NodeJs 构建通用 bot

> 原文：<https://dev.to/samueleresca/build-universal-bot-using-nodejs>

最初发布于 [https://samueleresca](https://samueleresca)

# 使用 NodeJs 构建通用 bot

微软最近发布了[机器人框架](https://dev.botframework.com/):这是一个非常有用的框架，可以构建和连接智能机器人，无论用户身在何处，都可以与他们自然互动，从 Telegram 到 Skype，Slack，Facebook 和其他流行的服务。

本文展示了如何使用 NodeJs 和[bot 框架](https://dev.botframework.com/)构建通用 Bot，目的是使用[微软认知服务](https://www.microsoft.com/cognitive-services)构建一个可以识别和描述图像的 Bot。

我已经在下面的文章中写了关于 bot 框架的内容。网。

演示代码[可在 Github](https://github.com/samueleresca/Blog.BotFrameworkNode) 上获得。

## 设置节点项目

首先，为您的 bot 创建一个文件夹，并使用以下命令初始化节点项目:

`npm init`

接下来，您需要通过运行以下命令来安装项目依赖项:

`npm install --save botbuilder
npm install --save restify`

Restify 是一个 node.js 模块，专门用于构建正确的 REST web 服务，botbuilder 包含 T2 Bot 框架 T3。

## 图表

下面是节点项目的简单示意图。它说明了项目的架构:

[![Universal bot development using NodeJs](img/71843ad8c6983ea6b6513ac8208afa90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kVqYlS6q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2016/10/Screen-Shot-2016-10-14-at-23.05.10-768x438.png)

## 密码

### ConfigurationHelper.js

`ConfigurationHelper.js`包含一个代表机器人配置的对象:

[https://gist . github . com/samueleresca/90 efacd 6873 b 93 B9 a7 B2 d 95 b 102 CD 2f 7](https://gist.github.com/samueleresca/90efacd6873b93b9a7b2d95b102cd2f7)

特别是:

*   包含你的机器人的 ID 和密码，可以在这里获得。
*   `COMPUTER_VISION_SERVICE`包含可在此获得的认知服务的 Url 和 API 密钥。

### BotHelper.js

`BotHelper.js`包含一些从输入消息中提取 URL 的 utils 方法:

[https://gist . github . com/samueleresca/0 c 55 b 6 da E1 ad 5b 7 a 94195 b 523622 cdfc](https://gist.github.com/samueleresca/0c55b6dae1ad5b7a94195b523622cdfc)

### VisionService.js

`VisionService.js`包含一些从微软认知服务中检索信息并提取机器人发送的响应的方法:

[https://gist . github . com/samueleresca/715 aa 0532822 AFC 769d 002 EB 0d 18 Fe 67](https://gist.github.com/samueleresca/715aa0532822afc769d002eb0d18fe67)

### app.js(入口点)

`app.js`是节点服务器的主要入口点，它运行 bot 使用的所有进程:

[https://gist . github . com/samueleresca/96 f 86 e 3b 021 c 303 CDF 99 aa 407d 35 f 94 f](https://gist.github.com/samueleresca/96f86e3b021c303cdf99aa407d35f94f)

## 部署项目

为了在消息传递平台上使用 bot，有必要在托管提供者上部署节点项目。有很多选项，比如: **AWS，微软 Azure 或者 Heroku** 。

在简单演示的情况下，我认为 Heroku 是最好的选择:它非常直接和简单。你可以将你的 Heroku app 与 github repository 连接，或者在服务器上上传源代码。

### 使用 bot 框架设置 bot

一旦你在服务器上部署了节点应用程序，你需要在下面的页面“注册”这个机器人:为了在所有支持的消息平台上发布这个机器人。

### 决赛成绩

[![Universal bot development using NodeJs](img/6ccef43236457d2a6d5e255bf09d8c0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SODVXTtP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2016/10/Screen-Shot-2016-10-15-at-12.01.55-1024x719.png)

## 最后的想法

bot 框架允许开发者使用 Node.js 或构建通用 Bot。NET 框架。

#### 为什么聊天机器人对你的业务很重要？

*   随时可用:当商家似乎只遵守银行营业时间时，消费者通常会感到恼火。消费者并非都在银行工作，他们需要能够在白天或晚上的任何时间联系公司寻求帮助；
*   将数据转化为个性化广告:机器人可以向你发送可购买的外观。根据你喜欢过或之前购买过的照片和产品，它可以向你发送产品推荐或发送店内购买优惠券；
*   自然语言交流:消费者需要相信他们是在和一个真人说话。聊天机器人被设计成专门对消费者的直接反应做出反应，并根据他们的需求提供合适的产品；

演示代码[可在 Github](https://github.com/samueleresca/Blog.BotFrameworkNode) 上获得。

干杯:)