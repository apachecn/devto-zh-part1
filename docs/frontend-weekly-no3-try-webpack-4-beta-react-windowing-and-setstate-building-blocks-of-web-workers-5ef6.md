# 前端+每周第 3 期:试用 Webpack 4 Beta，React Windowing 和 setState，Web Workers 的构建块

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no3-try-webpack-4-beta-react-windowing-and-setstate-building-blocks-of-web-workers-5ef6>

[![周报封面 49.jpg](img/3dd0ea608c5f1286ef8fcf4481a861e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aMx_Ev2S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-5931b2820439ccf4.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://github.com/wxyyxc1992/Coder-Knowledge-Management/blob/master/Weekly/README-en.md)中找到。

## 新闻

*   [iOS 11.3 和 macOS 10.13.4 包含服务人员](https://parg.co/Ui0):本周，Safari 技术预览版 48 可用，它改变了密码自动填充的工作方式，启用了存储访问 API 等。更重要的是，iOS 11.3 和 macOS 10.13.4 包括服务工作者，这是一个强大的规范，允许后台脚本为离线 web 应用提供动力。iOS 11.3 在将 web 应用添加到主屏幕时还会参考 Web 应用清单。更多关于 PWA 的参考可以在[这里](https://parg.co/UiT)找到。

*   [Parcel v1.5.0 发布](https://parg.co/Uiv) : Parcel 在 Github 上有超过 17，000 颗星，在 npm 上有超过 50 万次下载，有超过 60 名贡献者提交了超过 200 次拉取请求！Parcel v1.5.0 是一个巨大的版本。亮点包括:JavaScript 源码图支持、WebAssembly 支持、Rust → WebAssembly 支持、配置文件无效缓存、。env 文件支持，对于较小的项目甚至更快。

*   [Expo SDK v25.0.0 发布](https://parg.co/Ui9) : Expo SDK v25.0.0 基于最近发布的 React Native 0.52。在这个版本中，它支持独立应用程序中的字体、视频和音频资源；让发布渠道变得更强大；对 AWS Cognito 的现成支持；改进 API 和添加。

*   [Bootstrap 4](http://blog.getbootstrap.com/2018/01/18/bootstrap-4/) :我们花了好几年才完成，但 Bootstrap 4 终于来了！自从我们上一个测试版以来，没有重大的变化，但是我们已经做了一些关键的改进，并解决了一些棘手的错误:打印样式和实用程序类已经更新，添加了附加的边框实用程序，我们的`$spacers`和`$sizes` Sass 地图已经更新，我们的主题化文档添加了文档，添加了 responsive。顺序-0 和。对 flexbox 网格的更多控制。

## 教程

*   webpack 4 测试版—立即试用！:本周，webpack 4.0.0-beta.0 发布。这篇帖子陈述了 webpack 4 中的新功能:多场景下性能将显著增强，更好的默认值，支持副作用:package.json 中的 false，json 支持&树抖动，升级到 UglifyJS2，模块类型的引入+。mjs 支持，WebAssembly 支持，再见 CommonsChunkPlugin 等。更多关于 Webpack 的参考资料可以在[这里](https://parg.co/UkT)找到。

*   [如何使用 JavaScript 代理来获得乐趣和利润](https://parg.co/Uie):JavaScript 语言最近有一个新特性还没有被广泛使用:JavaScript 代理。使用 JavaScript 代理，您可以包装现有的对象并拦截对其属性或方法的任何访问。在本文中，作者使用 proxy 开发了一个包含 20 行代码的 sdk for api，用可读性更好的方法查询数据结构，监控异步函数。更多关于 JavaScript 的参考可以在[这里](https://parg.co/ULH)找到。

*   [未被采纳的有趣 ECMAScript 2017 提案](https://parg.co/UiW):由于 polyfills 和 transpilers 近年来变得流行，一些早期提案甚至在最终确定之前就已经获得了大量采纳。在本文中，我们将浏览一些未被采纳的有趣的 ECMAScript 2017 提案。首先，作者介绍了 ECMAScript 提案过程的五个阶段。接下来，他谈到了异步迭代、类改进、类装饰器、导入函数、可观察值、Do 表达式、可选链接等。更多关于 JavaScript 的参考可以在[这里](https://parg.co/ULH)找到。

## 工程实践

*   [使用窗口创建更高效的 React 视图](https://parg.co/UiL):在这个演示中， [bvaughn](https://github.com/bvaughn/) 讲述了 React 开发中的性能、搜索和应用架构。他重点讲述了是什么降低了 React 应用程序的速度，我们如何解决这些问题，并分享了一些经验教训。值得一提的是，他讨论了窗口是如何工作的，以及如何只创建用户可以看到的元素。更多学习 React 的参考可以在[这里](https://parg.co/UHK)找到。

*   SPA，还是不 SPA，这是个问题！:在这篇文章中，我将尝试分享我的经验，列出我认为你不应该做 SPA 网站的 10 个理由:速度和性能、高开发成本、脆弱性、难以长期维护、安全性差、重新发明轮子、社交媒体和元属性、可访问性差、浪费内存和能源。更多关于 web 架构的参考可以在[这里](https://parg.co/UiB)找到。

*   [现在你看到我了:如何推迟、延迟加载和与 IntersectionObserver](https://parg.co/Uiu) 一起行动:在这篇文章中，我们将走出滚动的黑暗，谈论延迟加载资源的现代方式。不只是延迟加载图像，而是加载任何资产。更重要的是，我们今天要讨论的技术不仅仅能够延迟加载资产:我们将能够基于元素对用户的可见性提供任何类型的延迟功能。更多关于网页优化的参考可以在[这里](https://parg.co/Uin)找到。

## 引擎盖下

*   [为什么`setState`是异步的？](https://parg.co/Uid):MobX 的作者 mweststrate 开这个问题想知道为什么 setState 是异步的。我们经常听到的理由是:异步渲染需要异步设置状态，异步设置状态需要知道渲染了哪个状态等等。Gaearon 用不同思想澄清了这种混乱:保证内部一致性、支持并发更新等。更多学习 React 的参考可以在[这里](https://parg.co/UHK)找到。

*   [JavaScript 如何工作:Web Workers 的构建模块+你应该使用它们的 5 种情况](https://parg.co/Uig):这是致力于探索 JavaScript 及其构建组件的系列文章的第 7 篇，我们将提供一个概述，讨论不同类型的 Workers，它们的构建组件如何一起发挥作用，以及它们在不同场景中提供的优势和局限性。异步函数只解决了 JavaScript 语言的一小部分单线程限制，另一方面，Web Workers 是轻量级的浏览器内线程，可以用来执行 JavaScript 代码，而不会阻塞事件循环。更多关于网络工作者的参考资料可以在[这里](https://parg.co/UiD)找到。

*   [Firefox 58:量子时代继续](https://parg.co/UiO) : 2017 年对 Mozilla 来说是重要的一年，最终发布了 Firefox Quantum，这是一项历时多年、专注于速度的浏览器大规模重组，并为未来几年奠定了基础。以下是最新 Firefox 中的一些改进，脱离主线程绘制，通过后台 Tab 调节、WebAssembly 流编译器、CSS 字体显示、减少冗余代码的新 Promise 功能来保持 Firefox 的专注。这些也是火狐 59 中的一些[扩展增强。](https://parg.co/Uis)

## 开源

*   Rejoiner : Rejoiner 由 Google 开源，从 gRPC 微服务和其他 Protobuf 源生成统一的 GraphQL 模式。它允许 GraphQL 模式被灵活地定义和组成为共享组件，基于 GraphQL 查询参数填充请求协议，提供 DSL 来修改生成的模式，通过注释获取数据的方法来连接数据源。

*   CSS Gridish : CSS Gridish 获取产品网格的设计规格，并为您的团队构建几个资源以供使用:为设计师提供带有画板和网格/布局设置的草图文件，为开发人员提供使用 CSS 网格和 CSS Flexbox 后备的 CSS/SCSS 代码，为任何人提供检查网页对齐情况的 Google Chrome 扩展。这个工具不是一个已经为你设计好的网格系统。相反，CSS Gridish 为您的团队设计的网格构建所有的资源。更多信息可以在[这篇文章](https://parg.co/Uim)中找到。

*   Greenlet 可以将一个异步函数移动到它自己的线程中，它可以被看作是一个简化的单一函数版本的 workerize。您传递的函数应该是纯函数，因为它们是在隔离的范围内执行的。

*   当你在建立一个网站的时候，你可能会使用一个 css 框架，比如 Bootstrap，Materializecss，Foundation 等等...但是你只会使用一小部分框架，并且会包含很多未使用的 css 样式。这就是 Purgecss 发挥作用的地方。Purgecss 分析你的内容和你的 css 文件。然后，它将文件中使用的选择器与内容文件中的选择器进行匹配。它从你的 css 中移除未使用的选择器，产生更小的 css 文件。