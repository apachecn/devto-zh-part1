# Almin + React/Vue 可以直观地优化性能

> 原文：<https://dev.to/azu/almin--reactvue-can-optimizing-performance-visually-541>

[![](img/36c710c3d7bccdf82edbe8b318c441a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1DSDYpW3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/625/0%2AcPszCCl-NvXbdngl.png)

[Almin](https://github.com/almin/almin) 是一个类似 Redux/Flux 的状态管理库。但阿尔明旨在帮助客户方 DDD/CQRS。

这种架构也被称为[分层架构](https://www.safaribooksonline.com/library/view/software-architecture-patterns/9781491971437/ch01.html)。

[![](img/faf062ddd8cf8f2a8dff4c40a2307b05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bv9mpzMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AGKnC9nZ8Z4PKHp52.png)

我们已经发布了 [Almin 0.14.0](https://github.com/almin/almin/releases/tag/almin%400.14.0) 支持基于 performance.mark 测量性能配置文件。您可以使用浏览器开发工具 timeline 分析用例执行、存储组写/读、存储更新。

[![](img/b47922ab667f6154aafd6670361bb8a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_-aj5al--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AOezdIE55AzltM6jf.png)

performance.mark 在[用户计时级别 2](https://w3c.github.io/user-timing/) 中定义为 Web 标准 API。这意味着 almin 与 React 和 Vue 等其他库在性能配置文件上协同工作。

### 用法

如果你没听说过 almin，请看[简介-almin . js](https://almin.js.org/)

您可以通过 performance profile 选项打开性能配置文件。

[https://gist.github.com/c40f4e11b9b0d131146b4a2c6ac7b546](https://gist.github.com/c40f4e11b9b0d131146b4a2c6ac7b546)

### 步骤

1.  打开`performanceProfile`选项
2.  加载你的应用
3.  打开浏览器 DevTools 的“性能”选项卡并按下 Record
4.  停止录制
5.  Almin 事件将被归入**用户计时* *标签下

实际上，你可以用 Chrome DevTool timeline 查看性能配置文件。

液体错误:内部

> 这个视频在时间轴上显示了 Almin + React。React 有[？进行性能分析的 react_perf](https://facebook.github.io/react/docs/optimizing-performance.html) 选项。

下面是示例代码。

*   [阿尔明/示例/主阿尔明/阿尔明的购物车](https://github.com/almin/almin/tree/master/examples/shopping-cart)

### 视图+ Almin

Vue 还支持基于 performance.mark 的[性能选项](https://vuejs.org/v2/api/#performance)

我已经创建了一个使用 Almin + Vue 的示例项目。

*   [azu/Vue-Almin-counter-example:Vue+Almin counter example](https://github.com/azu/vue-almin-counter-example)

[![](img/ae7b6d876ab3541ac9d28b11dc902c4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---qFG8JPn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/946/0%2A14HkBqiQTcztxpv6.gif)

*   Vue 的配置文件显示视图指标(渲染和修补)
*   Almin 的配置文件显示用例执行、存储组写/读、存储更新时间

度量标准的收集记录在以下文档中。

[![](img/f4f9a9a816ee28957325082d6cf3b250.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2_MdfOuq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/816/0%2AyZ4eGQ5XanPj1S5f.png)

*   [性能简介-almin . js](https://almin.js.org/docs/tips/performance-profile.html)

这个项目从 Vue + Vuex 迁移到 Vue + Almin。差异如下。

### 结论

Almin 可以使用浏览器开发工具测量性能概况。

*   [Chrome 的时间轴工具文档](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/timeline-tool)
*   [MSEdge 的 F12 开发工具指南](https://docs.microsoft.com/en-us/microsoft-edge/f12-devtools-guide/performance)

这个性能配置文件也可以与 React 或 Vue 等其他库一起使用。

您可以使用这些指标找到瓶颈。

有关更多详细信息，请参见以下文档。

*   [性能简介-almin . js](https://almin.js.org/docs/tips/performance-profile.html)
*   [用例 Almin.js 的生命周期](https://almin.js.org/docs/tips/usecase-lifecycle.html)

[阿尔金/阿尔金](https://github.com/almin/almin)