# React 入门提示

> 原文：<https://dev.to/aroach/tips-on-getting-started-with-react>

我一直在做一个新的节点项目。作为 Angular 1 的长期用户，在看了 Angular 2 之后，我决定尝试在前端使用 React。虽然 React 看起来很简单，但我认为它实际上并不简单。

所以现在开始。我试图根据我的大脑如何开始使用 React 来整理我的观点。如果你发现这篇文章，我希望它能节省你搜索例子和教程的时间。

***首先要了解的项目是*** [***创建-反应-app***](https://github.com/facebookincubator/create-react-app) ***。*** 这是一个用于创建 React app 的生成器。它是由脸书创建的，有大量的功能。它的好处是，它似乎允许你跳过设置 webpack。我从这开始，到目前为止还没有遇到任何限制或问题。(著名的遗言。)自述文件中的几个突出部分是:

*   [提供客户端路由的应用](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#serving-apps-with-client-side-routing)。我了解到，在节点应用程序中，你可以将你的备用路线放在你想要服务的路线之后。例如，我想在`/api`上提供我的 REST API，但是允许客户端处理其余的请求。因此，我只需要将我的`app.get('/api', function(req, res){...});`语句排序，将`app.get('/\*', function(req, res){...});`作为最后一个匹配的语句。
*   [代理开发中的 API 请求](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development)。由于我有一个 API 和客户端由同一个应用程序提供，我真的需要这个。
*   [添加自定义环境变量](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables)注意，这是一个替换，而不是运行时替换，因为 React 项目是构建的工件。

***忍痛学习***[***Redux***](http://redux.js.org/)***。*** 一开始我以为不需要 Redux 这样的东西来全局管理应用状态。一旦你理解了`connect`、`mapStateToProps()`等等，拥有一个全球国有商店让 React 对我来说更容易处理。当然，这完全取决于你的应用程序的复杂程度。这里有几个 Redux 视频系列和教程，我觉得很有价值:

这些视频来自 Redux 的创始人丹·阿布拉莫夫，非常优秀:

*   [第 1 部分:Redux 入门](https://egghead.io/series/getting-started-with-redux)
*   [第二部分:构建 React 应用](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)

Kurt Weiberth 在 React + Redux 上创建了他自己的[教程系列，虽然我在 Dan 的系列之前看过这些(我认为你应该从 Dan 的开始看)，Kurt 的也真的很有帮助！](https://www.youtube.com/playlist?list=PLQDnxXqV213JJFtDaG0aE9vqvp6Wm7nBg)

[React+Redux 真实世界示例](https://github.com/gothinkster/react-redux-realworld-example-app):正如你可能观察到的，上面的 Redux 示例处理的是一个 Todo list 应用程序，虽然我最近发现 Dan 教程的第二部分更真实，但我发现这个“真实世界”示例也很有帮助。

创建一个 CRUD React+Redux 是我第一次决定尝试 Redux 时遵循的例子。在 Sophie 的帖子中有很多好的信息，但是我想如果我在检查这个之前回顾一下 Dan 的系列，我会理解得更好。

我觉得我的应用程序需要客户端路由。部分原因可能是基于我对 Angular 的体验。因此， ***我鼓励大家去看看*** [***React 路由器 2.x***](https://github.com/ReactTraining/react-router/blob/v2.8.1/docs/guides/README.md) ***。这与 Angular 的 ngRoute 或 ui-router 的工作方式绝对不同，因此它帮助我找到了一些例子。***

我从全新的 React 路由器 4 开始。我设法使用版本 4 获得了我的应用程序的一个版本，但是我发现使用版本 4 的例子很少。我最终降级到 2.7，因为我遇到的大多数例子和教程都使用 2.x 版本。如果你确实想使用 4 版本，我在一篇博客文章中遇到了一个例子，并联系了作者 [@bodiddlie](https://twitter.com/bodiddlie) ，他[在推特上发布了他的 github repo](https://twitter.com/bodiddlie/status/852271980879396865) 的链接。Sophie 的 cat 目录(上面的 CRUD 例子)也使用 react router 2.x。

最后，我需要在 React 的 ***测试和 Redux 的*** 测试上投入更多的时间，并且发现[这篇文章](https://medium.com/javascript-inside/some-thoughts-on-testing-react-redux-applications-8571fbc1b78f)提供了一个很好的关于测试方法的实用总结。

非常感谢每个提供例子、文档和教程的人！

*原载于 2017 年 5 月 3 日*[*aroach . github . io*](http://aroach.github.io/2017/05/03/getting-started-with-react.html)*。*