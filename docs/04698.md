# 新插件，新版本-向 Express Gateway 1.2.0 问好！

> 原文：<https://dev.to/lunchbadger/new-plugins-new-release---say-hello-to-express-gateway-120>

自从 7 月份在旧金山 Node.js 峰会上正式发布以来，我们一直在努力与 [Express Gateway](www,express-gateway.io) 合作，这是一个完全基于 [Express.js](http://expressjs.com/) 构建的开源 API 网关。午餐獾不仅仅是我们。我们已经从[开发者社区](https://gitter.im/ExpressGateway/express-gateway)看到了一些非常有趣的反馈。因此，我们很高兴在这方面加大努力，并开始烧掉社区路线图中的积压工作。

现在，你已经掌握了它的要点——我们的最新版本(也在产品搜索上发布)包括一个可扩展的插件框架。

## 这到底是什么插件框架？

[插件](http://www.express-gateway.io/docs/plugins/)框架使开发者能够将 Express 中间件作为插件集成到 Express Gateway 中。

快速网关插件包含实体，也允许事件订阅。因此，一个解决这个问题的方法是把插件想象成一个快递网关实体的容器。他们通过添加实体和提供事件处理程序来扩展核心。

## 这对 Express Gateway 意味着什么？

因此，Express Gateway 1.2.0 增加了一个新的插件引擎，允许任何人在插件中添加自己的扩展实体。然后，您可以在 Express Gateway 中动态安装和使用它。
Express Gateway 插件充当一个容器，有三个关键要素:

*   Package.json
*   每个实体扩展对应一个文件夹
*   Manifest.js

与任何节点模块一样，package.json 声明了插件需要的所有依赖项。

我们开始分解所有的安装、实体扩展点文件夹、manifest.js，以及更多神奇的东西。

## 好奇自己造？

Express Gateway 是 ExpressJS 节点之上的一个薄层。JS 框架并使用了其中的许多概念。所以对它有一些概念是很好的，尤其是 Express.js 中间件。

要了解插件的不同部分何时注册和加载，请查看 [Express Gateway 引导序列解释](http://www.express-gateway.io/docs/runtime/boot-sequence)。

下面是简短的版本:

*   初始阶段*(配置加载，插件注册)*
*   快速网关初始化*(网关 ExpressJS 服务器初始化，加载网关扩展，管道引擎初始化，网关服务器启动，事件)*
*   管理 API 初始化

**~鳍**

您还可以查看更多扩展点，包括在[开发指南](http://www.express-gateway.io/docs/plugins/plugin-development)或[构建定制策略](http://www.express-gateway.io/docs/plugins/policy-development)中。这里是 Github 上的一个[示例插件，所有这些令人敬畏的扩展点都被利用了。](https://github.com/ExpressGateway/express-gateway-plugin-example)

在它结束之前，如果你想去 **[看看产品搜索](https://www.producthunt.com/posts/express-gateway)** 的最新版本，并分享你对这个项目的想法，新版本。