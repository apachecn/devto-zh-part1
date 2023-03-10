# 为什么是 Sails.js？

> 原文：<https://dev.to/prestonjlamb/why-sails-js-2e3i>

今天我有机会在犹他州普罗沃的 [Dev Mountain](https://devmounta.in) 做了一个演讲，主题是为什么你应该使用 [Sails.js](https://www.sailsjs.com) ，一个用于 Node 的 MVC 框架。原因有很多，但我想分享一些我最喜欢的。

## 蓝图

Sails 的一个真正有助于入门的特性是它的 Blueprints API。Blueprints API 允许您快速启动并运行 RESTful API，只需很少的实际代码。使用 CLI 创建新蓝图:

```
sails generate api User 
```

Enter fullscreen mode Exit fullscreen mode

生成一个模型文件(`api/models/User.js`)和一个控制器文件(`api/controllers/User.js`)。此外，它还提供了一些基本操作，比如获取用户列表或创建新用户。要获得用户列表，只需向`/user`发出 get 请求，就会得到作为对象数组的响应。不需要额外的代码。创建:`/user/create?name=Name&amp;age=20`。就是这样。

显然，您希望创建功能与此稍有不同。此外，您需要设置数据库连接，以便它存储在正确的位置，但这仍然可以节省一些开箱即用的工作。

## 政策

我喜欢使用的另一个 Sails 特性是策略。策略可用于阻止对应用程序不同部分的访问。策略是一个简单的函数，它有 3 个参数:请求、响应和下一个回调。如果满足所需的条件，则调用`next()`回调。否则，您可以选择返回一条错误消息。

Sails 还可以非常轻松地将策略分配给应用程序中的所有路线、某些控制器，甚至控制器上的单个功能。政策是风帆非常强大的一部分。

以我的经验来看，我们对功能使用了策略，而不仅仅是认证和访问应用程序。对于某些路由，我们需要存储到会话中的关于用户的更多信息以供重用，并且我们不希望继续一次又一次地调用来获取这些信息。所以我们使用策略调用数据库，获得一次信息，然后保存到请求中。

## 路线

在 Sails 中，向您的应用程序添加路线，无论是为了视图还是 API 端点检索数据，都非常简单。在`config`文件夹中的一个文件`config/routes.js`中声明了所有的路由。申报路线就这么简单:

```
'GET /api/route': 'SomeController.functionOnTheController' 
```

Enter fullscreen mode Exit fullscreen mode

简单地声明一个返回视图的路径:

```
'/': { view: 'homepage' } 
```

Enter fullscreen mode Exit fullscreen mode

不管怎样，在 Sails 中，路由声明和生成 RESTful API 真的很容易。在简单的`routes.js`文件和 Blueprints API 之间，很容易将数据传输到您的应用程序。

## 水线

水线是作为框架的一部分提供的 ORM/ODM。它是一个与数据存储无关的工具，允许您查找、创建、更新或删除数据，而无需编写特定于供应商的代码。因此，换句话说，无论您使用的是 SQL 数据库还是非 SQL 数据库，您都可以用相同的方式编写数据库访问函数。对于那些使用猫鼬，这是同样的想法。

您唯一要做的事情就是声明您想要在您的 Sails 项目中使用哪个数据库适配器。事实上，如果您愿意，每个模型都可以使用不同的数据库。你可以通过 NPM 安装这些适配器。Waterline 允许你做的一件很酷的事情是跨数据库连接。您可以在 Waterline 中从一个模型到另一个模型进行连接，如果一个模型保存在 SQL Server 中，而另一个模型保存在 Mongo 中，您仍然可以进行连接。查看文档以获得更多相关信息。

## 结论

喜欢 Sails.js 的理由有很多，这只是我喜欢它的 4 个理由。而且，我只是勉强触及了其中的每一个想法。我绝对推荐在你的任何项目中使用 Sails.js。我用过之后感觉很棒。社区很棒，核心团队也很棒。我已经从 StackOverflow 和该框架的创建者 Mike McNeil 那里得到了回应。一定要让我知道你是否决定使用这个框架，或者你是否使用其他的框架！我喜欢学习更多的东西，喜欢看别人在做什么。