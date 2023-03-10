# 用 Jest 测试 AngularJS 应用程序

> 原文：<https://dev.to/swiip/testing-an-angularjs-app-with-jest-20a3>

[![](img/9cf16349ca58d9654219a5343f4bf153.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JGJtdpxb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/941/1%2AbsQsjWp1fzdBtP8hxUGKEw.png)

我不会尝试用 Jest 编写测试 AngularJS 应用程序的最终指南，这篇文章只是我在我的项目中尝试 Jest 后的反馈。

**TL；博士**效果很好:)

首先，让我补充一些背景。我是 AngularJS 的超级粉丝，慢慢漂移才反应过来。这可能是我意识到 Jest 并想尝试它的原因。尽管如此，我正在进行的最大项目使用 AngularJS，它是新数据交换平台 Dawex([https://www.dawex.com/](https://www.dawex.com/))的成员部分

另外，我是 Yeoman Fountain generator([fountainjs . io](http://fountainjs.io))的开发人员之一，因此，我非常熟悉新 Web 工具的使用和配置。

我认为，这个应用是一个非常现代的 AngularJS 应用。它使用 AngularJS 1.5.x 完全面向组件，并使用 NPM、Webpack 和 Babel 作为工具。我们甚至增加了一些新的东西，比如 RxJS 和 Redux。

测试环境基于 Karma 和 Jasmine。我们使用 karma-webpack 插件，测试在 Chrome 上运行，因为我们在 PhantomJS 上遇到了一些问题。

测试工作与观看模式和覆盖，但仍有一些痛点。测试引导非常慢，因为 Webpack 必须构建整个包。然后，它启动了一个真正的 Chrome(需要在 CI 上使用 xvfb 的技巧),我们没有找到一种方法(通过 Babel / Webpack / Karma)来进行正确的错误堆栈跟踪。最后，但也许是最重要的，全球运行时间似乎很慢(接下来是真实数据)。

这是 Karma 配置的摘录。