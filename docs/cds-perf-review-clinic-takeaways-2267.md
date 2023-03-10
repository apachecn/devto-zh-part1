# CDS 绩效评估诊所外卖

> 原文：<https://dev.to/jeffposnick/cds-perf-review-clinic-takeaways-2267>

# CDS’17 回顾展

[Chrome Dev Summit 2017](https://developer.chrome.com/devsummit/) 已经过去了。我花了大部分时间准备我的[“工具箱:灵活的 PWA 图书馆”演讲](https://www.youtube.com/watch?v=DtuJ55tmjps)，但除此之外，我还参与了[运营现场绩效评估诊所](https://twitter.com/jeffposnick/status/922899094053330944)，参与者可以报名参加。

与开发我每天使用的 web 应用程序的开发人员见面，并提供我所能提供的任何指导，总是一种特权。观察一大群生产网站的表现会给你一些深刻的见解，这些见解是你不能从仅仅一次性地检查一个网站中得到的。模式开始出现在需要改进的公共领域。

我想公开分享一些基于这些模式的高级分组，并为每个领域确定一些包含有用指导的链接。这绝不是详尽的，但我可以说，这些链接中的每一个都包含了我们在性能评审诊所中发现的现实世界性能问题的解决方案。

# Linkdump！

非常感谢所有的作者，他们中的许多人我有幸与之共事。

## 整体迁移/端到端案例研究

*   [反应和预测渐进式 Web 应用性能案例研究:Treebo](https://medium.com/dev-channel/treebo-a-react-and-preact-progressive-web-app-performance-case-study-5e4f450d5299)

## 加载和优先排序

*   [HTTP/2 简介](https://developers.google.com/web/fundamentals/performance/http2/)
*   [资源优先级-让浏览器帮助您](https://developers.google.com/web/fundamentals/performance/resource-prioritization)
*   [深入脚本加载的浑水](https://www.html5rocks.com/en/tutorials/speed/script-loading/)

## JavaScript

*   你买得起吗？:现实世界的网络性能预算
*   [JavaScript 启动性能](https://medium.com/reloading/javascript-start-up-performance-69200f43b201)

## CSS

*   [`critical`:提取 HTML 页面中&内联关键路径 CSS](https://github.com/addyosmani/critical)
*   [加载 CSS 的未来](https://jakearchibald.com/2016/link-in-body/)
*   [`loadCss`:异步加载 CSS 的函数](https://github.com/filamentgroup/loadCSS/)

## 缓存

*   [HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
*   [缓存最佳实践&最大年龄陷阱](https://jakearchibald.com/2016/caching-best-practices/)
*   [服务人员:介绍](https://developers.google.com/web/fundamentals/primers/service-workers/)
*   [工具箱:渐进式网络应用的 JavaScript 库](https://developers.google.com/web/tools/workbox/)

## 图片/多媒体内容

*   [基本图像优化](https://images.guide/)
*   [使用交叉点观察器惰性加载图像](https://deanhume.com/home/blogpost/lazy-loading-images-using-intersection-observer/10163)
*   [内联 SVG vs 图标字体【cage match】](https://css-tricks.com/icon-fonts-vs-svg/)
*   [内容跳转(以及如何避免)](https://css-tricks.com/content-jumping-avoid/)
*   [在网站上嵌入 YouTube 视频的更好方法](https://www.labnol.org/internet/light-youtube-embeds/27941/)