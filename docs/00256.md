# 前端+每周第 2 期:木偶师 1.0 发布，保持网络包快速从松弛，Prisma

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no2-puppeteer-10-released-keep-webpack-fast-from-slack-prisma-o9k>

[![周报封面 48.jpg](img/209ad6d7b9a0bfaca441a3bef4c97e08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lIPDkVW5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-791110b4c5c94053.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://github.com/wxyyxc1992/Coder-Knowledge-Management/blob/master/Weekly/README-en.md)中找到。

## 新闻

*   [速度计 2.0 发布](https://parg.co/U8N):2014 年，苹果公司的 WebKit 团队发布了速度计 1.0，这是一个 web 应用程序响应能力的基准。上周，速度计 2.0 发布了，它是现代 web 应用程序响应的基准工具，经过修改和改进，符合今天的标准。

*   [puppeter 1.0 发布](https://parg.co/U8n) : Chromium 65，JS/CSS 代码覆盖 API，PDF 定制，XPath 支持，raw devtools 协议访问。

*   [jQuery 3.3.0 发布](https://parg.co/UkP):距离上一次 jQuery 发布已经有一段时间了，但是核心团队并没有闲着，他们更专注于哪些东西可以去掉，而不是可以添加。在新版本中。addClass()，。removeClass()和。toggleClass()方法现在接受一组类。

*   [Angular 5.2 现已上市](https://parg.co/UkF):Angular 5 . 2 . 0 版本已经发布。这是一个次要版本，是包含许多错误修复的 5.1 的替代版本。在这个版本中，他们改进了模板的类型检查，添加了对 TypeScript 2.6 的支持，并改进了路由器参数&数据继承。

## 教程

*   Vue Devtools 4.0 的新功能:就在几天前，Vue Devtools 发布了一个重大更新，在这篇文章中，我们将深入探讨新功能和改进。变化包括可编辑的组件数据，在编辑器中打开组件，显示原始组件名称，检查组件变得更容易，按组件过滤事件，可折叠的检查器等。

*   节省调试时间的 CSS 命名惯例:维护 CSS 非常困难，写得不好的 CSS 会很快变成一场噩梦。这里有几个命名惯例，可以帮你减轻一点压力，节省大量时间。

*   [仅使用 CSS(不使用 JS)进行网页跟踪](https://github.com/jbtronics/CrookedStyleSheets):本文是仅使用 CSS 而不使用 Javascript 进行网站跟踪/分析的概念证明。我们可以收集一些关于用户的基本信息，使用这种方法，可以跟踪用户第一次访问某个链接或第一次悬停在某个字段上的时间。

*   [将 create-react-app 项目升级为 SSR +代码分割设置](https://parg.co/Ukg):从一开始，create-react-app 就是一个快速原型化 react 应用、演示和测试不同特性或技术的伟大工具。幸运的是，我们可以改进事情，即使没有弹射。让我们试着一步一步来做这件事。这就是我们将要讨论的内容:服务器端渲染，使用 react-loadable 进行代码拆分，在服务器上进行代码拆分，利用 webpack 的 chunkNames。

## 工程实践

*   [保持 webpack 快速运行:提高构建性能的现场指南](https://parg.co/UkI) : Webpack 是捆绑前端资产的出色工具，但它包含电池的特性和第三方工具的海洋使其难以优化。这篇文章是一个现场指导，提供了我们在通往更快构建的道路上所学到的东西。Slack 的人分享了他们在测量构建成本、并行化构建过程、减少文字负载、使用缓存、硬件投资等方面的经验。

*   [CSS Grid + Flexbox 解决现实世界的问题](https://parg.co/Ukv):最近我得到了一个响应式设计，它看起来很复杂，在不同的视窗中，项目改变顺序，布局改变。这个新设计看起来很复杂，直到我们从 CSS Grid 和 FlexBox 的角度考虑它。这方面的挑战是支持 Internet Explorer 11 和 Safari 9+。在强调了设计的复杂性之后，我们将展示支持现代浏览器所需的 CSS，然后我们将添加 IE 支持，最后让它在 Safari 9 + 10.0 中工作。

*   单元测试时要避免的五个陷阱 Vue.js :单元测试本身就是一项技能，当你在学习一门新语言或一个新框架时，它可能不是你的首要任务。在博客中，作者将通过 Vue.js 示例向您展示您在日常工作中可以使用的具体步骤，并帮助您避免五个陷阱:等到最后，测试错误的东西，测试双精度，结构耦合，测试一切。

## 引擎盖下

*   [了解 React 源代码](https://parg.co/UVD) : React 提供了一种简单直观的方式来编写前端应用程序，所有移动部件都以状态的形式汇聚在一起。本系列主要关注 React 的内部工作方式。第一篇文章将通过呈现一个简单的组件，然后是一个类组件，开始遍历 React 的一个关键路径。

*   [节点如何加载内置模块](https://parg.co/Ukj):在之前的博客中，作者开始看节点主进程是如何初始化的。那里发生了很多事情，其中一件特别引起我注意的事情是对一个函数的引用，该函数似乎在节点主进程的初始化阶段加载内置模块。

*   [我从来不知道存在的 JS 事物](https://parg.co/UkL):前几天我通读了 MDN 文档，发现了这些我从来不知道存在的 JS 特性和 API。这里有一个简短的清单，列出了这些有用或无用的东西——学习 JS 似乎永无止境。有标签语句、“void”运算符、逗号运算符、带条件运算符、国际化 API、管道运算符、setTimeout()参数等。

## 开源

*   [After.js](https://github.com/jaredpalmer/after.js) : Next.js 类框架，用于使用 React Router 4 构建的服务器渲染 React 应用。Next.js 很棒，但它的路由系统并不令作者满意，所以他构建了 After.js，其目标是路由只是组件，与文件夹结构没有/不应该有任何关系。

*   [对话框-多填充](https://github.com/GoogleChrome/dialog-polyfill):对话框-多填充. js 是`<dialog>`和`<form method="dialog">`的多填充。`<dialog>`是网页中弹出框的一个元素，包括一个模态选项，在使用时会使页面的其余部分不活跃。这有助于阻止用户的交互，直到他们给你一个响应，或者确认一个动作。参见 [HTML 规范](https://html.spec.whatwg.org/multipage/forms.html#the-dialog-element)。

*   Scrollama : Scrollama 是一个现代的&轻量级 JavaScript 库，使用 IntersectionObserver 进行 scrollytelling，支持滚动事件。

*   JARVIS : J.A.R.V.I.S .(一个非常智能的系统)会把你需要的所有相关信息从你的 webpack 构建放到你的浏览器中，无论是在开发中还是在生产中。它极大地受到了其他 webpack 仪表板的启发，核心思想并不是原创的，但这里有一些功能:向您显示 ES Harmony 模块导入的数量，这些模块可以是树摇动的，而 CJS 模块不能，向您显示您的资产在 12 种不同的连接类型中的表现如何。

*   [1Backend](https://github.com/1backend/1backend) : 1Backend 是一个平台，旨在简化 lambda 功能/微服务的部署、运行和维护。它使您能够在几秒钟内启动一个新的实时应用程序-在选择您的技术堆栈(例如，访问 SQL 数据库)后，您会得到一个空的应用程序，该应用程序已经是实时的，可以从外部(通过 HTTP)调用。目前，它支持 Go、JavaScript、TypeScript 和 MySQL。

*   Prisma 可以把你的数据库变成一个 GraphQL API。Prisma 让您可以设计您的数据模型，并在几分钟内在线拥有一个生产就绪的 GraphQL API。Prisma GraphQL api 提供了强大的抽象和构建模块来开发灵活、可扩展的 GraphQL 后端:类型安全 api、使用声明性 sdl 的数据建模、实时 API、高级 API 组合，并可与所有前端框架配合使用。