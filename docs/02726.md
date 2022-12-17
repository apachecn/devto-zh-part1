# 通过 performance.mark 使用元数据进行 WebApp 性能监控

> 原文：<https://dev.to/azu/webapp-performance-monitoring-by-performancemark-with-metadata-d1l>

我已经创建了[性能标记元数据](https://github.com/azu/performance-mark-metadata)。该库提供了带有自定义元数据的 performance.mark。

这个库的灵感来自于[用户定时 API Level 3](https://docs.google.com/presentation/d/1d64Y4rtLCxobGgljVySU2CJpMPK5ksaiZuv3ka1dCVA/edit#slide=id.p) 的提议。该提案将细节作为元数据添加到 performance.mark 中。

目前， [Performance.mark()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API 无法向标记添加元数据。

### 支持

旧浏览器需要 Performance.mark() polyfill。

*   [nicjansma/user timing . js:user timing poly fill](https://github.com/nicjansma/usertiming.js)

### 安装

用 [npm](https://www.npmjs.com/) :
安装

```
npm install performance-mark-metadata 
```

Enter fullscreen mode Exit fullscreen mode

### 举例