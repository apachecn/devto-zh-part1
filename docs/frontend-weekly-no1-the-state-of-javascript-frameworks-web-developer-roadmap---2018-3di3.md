# 前端+每周第一期:JavaScript 框架的状态，Web 开发者路线图- 2018

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no1-the-state-of-javascript-frameworks-web-developer-roadmap---2018-3di3>

[![](img/2eabc0491f1d7c2a03442c91df146a1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--34AkU8Xi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-d2946f9aac541857.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

# 前端+每周第一期:JavaScript 框架现状，Web 开发者路线图- 2018

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://github.com/wxyyxc1992/Coder-Knowledge-Management/blob/master/Weekly/README-en.md)中找到。

## 新闻

*   [JavaScript 框架的状态，2017，作者 NPM](https://parg.co/UVE) : Npm 的人通过查看一个包的下载次数占当时注册表中所有包的下载次数的百分比(“注册表的份额”)，来挖掘一个包的增长速度是比注册表快(越来越受欢迎)还是比注册表慢(越来越不受欢迎)。这篇文章关注前端框架、React 生态系统和后端框架。对于前端框架，Preact 和 Vue 增长非常快，react 就其规模而言既庞大又增长非常快。对于 react 生态系统，Apollo 增长非常快，MobX 增长良好，但尚未挑战 Redux。

*   [Nuxt.js 1.0 出了](https://parg.co/UtZ) : Nuxt.js 是在 Vue.js、vue-router、vuex、vue-meta 之上的一个框架。Nuxt 1.0.0 出来了，最新版本的 Vue，vue-router，vuex 和 vue-meta。更好的稳定性、性能和生产准备。它还带来了一些突破性的变化(node > 8 兼容性)，新功能和许多错误修复。

*   [Node v9.4.0(当前)发布](https://parg.co/UV5):显著变化包括弃用异步敏感 API 和 runInAsyncIdScope，移除 reaches into _ events 内部，在 clientError 事件的 err 中添加 rawPacket 等。你可以查阅原文以获得更多信息

*   [新的软件包命名规则](https://parg.co/UVh):为了减少软件包的域名仿冒，如果已经有一个现有的软件包，npm 注册中心不再允许任何类似的命名。实际上，这是一个积极的推动和鼓励，让人们使用他们的用户范围来发布包。比如因为 react-native 的存在，没有人可以发布 reactnative 这样的变体，但是可以使用@ceejbot/json-stream 这样的作用域。

## 教程

*   [HTML 5.2 有什么新功能？](https://bitsofco.de/whats-new-in-html-5-2/):不到一个月前，HTML 5.2 成为 W3C 官方推荐标准(REC)，这意味着 W3C 官方推荐它由用户代理部署，由网页作者实现。在本文中，作者将回顾一些对我的开发影响最大的变化，比如原生对话框元素、来自 iFrames 的支付请求 API 和一些新的有效实践。更多关于 HTML 的参考可以在[这里](https://parg.co/UUK)找到。

*   [使用 typeScript 的实时应用程序:集成 Web Sockets，Node & Angular](https://parg.co/UVr) :在本文中，作者将向您展示如何仅使用 TypeScript 语言构建实时聊天应用程序。本文将介绍 webSocket，使用 express 和 typescript 实现聊天服务器，使用 angular 构建客户端应用等。更多关于打字稿的参考可以在[牛逼参考](https://parg.co/b4z)中找到。

*   [Node.js + face-recognition.js:使用深度学习的简单而鲁棒的人脸识别](https://parg.co/UVP):在这篇文章中我将向你展示如何使用 [face-recognition.js](https://github.com/justadudewhohacks/face-recognition.js) 来执行鲁棒的人脸检测和人脸识别。这个 npm 包在幕后使用 dlib，并将 Node.js 绑定暴露给 dlib 的人脸识别工具，dlib 库使用深度学习方法，并附带一些预训练的模型，这些模型在运行 LFW 人脸识别基准测试时已经显示出 99.38%的惊人预测精度。Node.js 的更多参考可以在[牛逼参考](https://parg.co/b4z)中找到。

## 工程实践

*   [服务器渲染、代码拆分、React 路由器 v4 懒加载](https://parg.co/UVJ):本文中，来自 Airbnb 的软件工程师分享了服务器渲染和代码拆分相结合的做法，以便在渲染前与你当前的路线进行匹配。更多关于 React 的参考可以在[牛逼参考](https://parg.co/b4z)中找到。

*   [2018 年打造更好 Node.js 应用的 8 个技巧](https://parg.co/UV8):在这篇文章中，来自 RisingStack 的家伙们收集了一些我们认为 Node.js 开发者在 2018 年应该遵循的技巧。它们是使用 async - await，熟悉 import 和 import()，熟悉 HTTP/2，消除代码风格争议，保护 Node.js 应用程序，等等。Node.js 的更多参考可以在[牛逼参考](https://parg.co/b4z)中找到。

*   [Web 开发者路线图- 2018](https://github.com/kamranahmedse/developer-roadmap) :在这个存储库中，您可以找到一组图表，展示您可以采取的途径以及您想要采用的技术，以便成为前端、后端或 devops。更多 Web 参考资料可以在[牛逼参考资料](https://parg.co/b4z)中找到。

## 引擎盖下

*   [现代网络负载平衡和代理介绍](http://t.cn/RQAfr5x):在这篇文章中，作者试图通过提供现代网络负载平衡和代理的温和介绍来纠正信息的缺乏。这篇文章包括什么是网络负载平衡和代理，负载平衡器与代理，不同的负载平衡器拓扑结构等。更多微服务参考可以在[牛逼参考](https://parg.co/b4z)中找到。

*   [操作系统如何工作:作为开发者你应该知道的 10 个概念](https://parg.co/UVV):在这篇文章中，作者分享了如果你想擅长开发软件，你需要学习的 10 个关键的操作系统概念。它们是进程和进程管理、线程和并发、调度、内存管理、输入/输出管理、虚拟化、分布式文件系统、分布式共享内存、云计算等等。更多操作系统参考可以在[牛逼参考](https://parg.co/b4z)中找到。

*   2018 年的 Frontend:更多共识，更少复杂性【React 和 Angular 这样的框架继续在社区中获得大规模支持，但 Vue 这样的新竞争者也在流行中爆发。Webpack 仍然是构建工具的首选，NPM 是软件包系统的首选。WebAssembly 以前所未有的速度向众多令人兴奋的新用例打开了 web。像 GraphQL 这样的技术革新了 API 在 web 应用程序中的编写和使用方式。

## 开源

*   Docusaurus 是一个用于轻松构建、部署和维护开源项目网站的项目。它声称开始简单，可本地化和可定制。

*   toapi 是一个聪明、简单和快速的 flask 库，使得任何网站都可以提供 api 服务。Toapi 把这些事情变成了小菜一碟。你需要做的就是定义你想要的数据，你已经做到了。流程全自动化，几秒钟就可以通过 API 访问数据！

*   TOAST UI 编辑器 :TOAST UI 编辑器是一个高效且可扩展的 Markdown WYSIWYG 编辑器，提供 Markdown 模式和 WYSIWYG 模式。TOAST UI 编辑器尊重 CommonMark 和 GFM 规范，alse 附带了符合 Markdown 语法的强大扩展，同时还提供 API，以便您可以开发自己的扩展。

*   Nerv 是一个基于虚拟 dom 的 JavaScript (TypeScript)库，具有相同的 React 16 API，提供了更高的性能、更小的包大小和更好的浏览器兼容性。

*   Worker ize:Worker ize 将一个模块移入一个 Web Worker，自动将导出的函数反映为异步代理。它将一个微小的、专门构建的 RPC 实现捆绑到您的应用程序中，支持同步和异步工作函数，并与 async/await 完美配合

*   [截屏转代码(screen-to-code-in-Keras)](https://github.com/emilwallner/Screenshot-to-code-in-Keras):将截屏转化为静态网站的神经网络。三年内深度学习将改变前端开发。它将提高原型开发速度，降低构建软件的门槛。在提供的模型中，我们将教会神经网络如何基于设计模型的图片来编写基本的 HTML 和 CSS 网站。