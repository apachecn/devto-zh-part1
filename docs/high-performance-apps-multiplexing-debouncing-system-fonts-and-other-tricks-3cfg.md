# 高性能应用:多路复用、去抖动、系统字体和其他技巧

> 原文：<https://dev.to/atila/high-performance-apps-multiplexing-debouncing-system-fonts-and-other-tricks-3cfg>

这里有一些客户端应用程序的性能提示，您可以立即开始使用。

这些将显著提升你的应用程序的感知性能。其中大多数只需要快速调整你的应用程序。

### 预载您的资源

rel="preload "是一种让你的浏览器知道特定资源很重要的方式。这样，您的浏览器将尽快获取资源。然后它将把它存储在本地，直到在 DOM 中找到期望的引用。

以下是使用具有此属性的链接的一些示例: