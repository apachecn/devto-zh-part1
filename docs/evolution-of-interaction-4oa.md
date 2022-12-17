# 相互作用的演变

> 原文：<https://dev.to/apastuhov/evolution-of-interaction-4oa>

最初发表于我的博客[apastuhov.com](http://apastuhov.com/)

* * *

如果你写前端应用程序-我假设它与后端 API 服务器交互。如果你的应用很小，你不会考虑架构和伸缩性。但是如果它是一个大的 LTS 应用程序，你需要考虑一下！为什么？因为您将在未来开发和支持该应用程序，所以祈祷您不会在将来讨厌自己吧。我想告诉你人们经常如何开发 UI 和 API 之间的交互。

为了达成一致，让我们定义**交互** -是一个对象/类/方法/函数，它允许你向后端服务器发送请求。

## 没有银弹或建筑类型

你可以尝试搜索一些解决方案！你可能会找到一些指南或库，也可能是你使用的框架的一部分，比如 [angular。$resource](https://docs.angularjs.org/api/ngResource/service/%24resource) 。一般来说，会有 4 种类型的*交互*架构。

*   飞行中
*   工厂
*   一个
*   “微观”服务

所以你会发现没有什么灵丹妙药，会是最好的或者最受欢迎的。

我不想描述每种方法的实现，因为这是你的工作。:)我想展示你将如何使用它，利弊。

## 即时或 jQuery 风格

[![JQuery.. JQuery everywhere](img/d8ad0140612d4457db62dba1a7c9eb6a.png "JQuery.. JQuery everywhere")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5mKwY7_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/buzzwoody.jpg)

我想你还记得 jQuery 有多流行，也许它仍然是某些人的最佳工具。jQuery 为我们提供了大量的好东西，其中之一是 AJAX 的简短符号。没有更多的 XmlHttpRequest，只是简单的`$.ajax`。

所以第一种方式是在需要时立即编写 AJAX 查询。

```
jQuery.ajax.get(
  'http://super-app.com/api/client',
  {
    success: function(data) {
      client = data;
    },
    error: function() {
      console.error('blah blah blah');
    }
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

我甚至不想讨论为什么这种方式不好。如果你试着用这样的方式写 SPA，你会发现你所有的请求都分散在你的应用程序中。如果你犯了一个小错误，你会发现它，只有当用户执行这个查询的时候。

和往常一样——有一天有人会说“我们需要一些时间来重构”。

## 工厂

[![Factory](img/440e84a85c8dabffa55a4325d31a78a1.png "Factory")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tsWdE8-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://apastuhov.cimg/factory.gif)

现在我们有了所有请求的配置，您可以在需要时动态创建它们。比如 angular 1.x 中的`resource`，可能有人会说那个例子不好，但是我要注意，实现可能会有所不同。工厂的思想——根据某些配置动态地生成对象(在我们的例子中是请求)。

让我们想象两个最流行的界面配置:

```
request.setup({
  host: 'http://super-app.com/api/'
}); 
```

Enter fullscreen mode Exit fullscreen mode

用法#1:

```
request(
  'GET',
  'client',
  function(data) {
    clientData = data;
  },
  function() {
    console.error('error handler');
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

用法#2:

```
var client = request('client');
client.get(
  function(data) {
    clientData = data;
  },
  function() {
    console.error('error handler');
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

## 单胎

[![S - Singleton](img/4407ea29d7d3d287acaa4d027bd035cc.png "S - Singleton")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rKgkbwSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/superhero.jpg)

Singleton -是软件开发中最流行的模式。所以我们有一个单独的类来保存所有的请求和配置。

```
api.getProjectList();
api.getProjectById(1);
api.updateClient(instance);
api.deleteUser(1);
// ... and so on 
```

Enter fullscreen mode Exit fullscreen mode

方法还不错，但`api.js`会随着时间的推移而成长。因此，更改和添加新的查询变得非常困难。咬紧牙关你会给文件添加新的方法，有一天你会决定你需要拆分它。

我假设您的架构将是下一个:

```
-/ api
 |- client.js
 |- project.js
 |- user.js
  \ api.js 
```

Enter fullscreen mode Exit fullscreen mode

用法将更改为下一个:

```
api.project.getList();
api.project.getById(1);
api.client.update(instance);
api.user.delete(1);
// ... and so on 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很好，也可以理解，但是有一个小问题——竞赛的分离。因此，使用项目 API——您可以访问客户端 API，并且可以删除它，而不是项目。这就是为什么你需要隔离类。

## 【微】服务

[![M - Many singletons](img/9051e6c131de7c8466d6c51d6e2cbe08.png "M - Many singletons")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JaXMRxhg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/superheroes.jpg)

不等等。我不想谈论一些分裂一切的大变化。我只想指出，每个像`./app/api/client.js`这样的文件都是一个服务。我们不需要那个单独的成员来保持所有服务的组合。

```
projectApi.getList();
projectApi.getById(1);
clientApi.update(instance);
userApi.delete(1);
// ... and so on 
```

Enter fullscreen mode Exit fullscreen mode

看来最后一个解决方案是理想的。但是您拥有的服务越多，您需要的导入和依赖就越多。有的可以说你需要按逻辑分开，做一些群服务。但这是一个漫长的讨论过程。

## 总结

每个解决方案都有优点和缺点，只有你自己才能定义什么与你相关。

我告诉过你如何开发 UI 和 API 之间的交互。我希望你明确了什么方法最适合你！写下你的评论，让我知道！

附注:

我想提一下，你写得越多——你就越是重复自己和别人。谁知道呢，也许有一天我们会回到简单的 jQuery 风格。:)