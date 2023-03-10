# 带有元数据的“performance.mark”对于真实用户监控非常有用

> 原文：<https://dev.to/azu/performancemark-with-medata-is-useful-for-real-user-monitoring-54p>

我已经创建了[性能标记元数据](https://github.com/azu/performance-mark-metadata)。
该库为`performance.mark`提供定制元数据。

这个库的灵感来自于[用户定时 API Level 3](https://docs.google.com/presentation/d/1d64Y4rtLCxobGgljVySU2CJpMPK5ksaiZuv3ka1dCVA/edit#slide=id.p) 的提议。
该提案将`details`作为元数据添加到`performance.mark`中。

目前， [Performance.mark()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API 无法向标记添加元数据。

## 支持

*   现代浏览器和 Node.js >= 8.5.0
*   它们被支持 [Performance.mark()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API。
*   [性能计时 API | Node.js v9.2.0 文档](https://nodejs.org/api/perf_hooks.html)

旧浏览器需要`Performance.mark()`填充。

*   [nicjansma/user timing . js:user timing poly fill](https://github.com/nicjansma/usertiming.js)

## 安装

用 [npm](https://www.npmjs.com/) 安装:

```
npm install performance-mark-metadata 
```

Enter fullscreen mode Exit fullscreen mode

## 举例

```
import { PerformanceMetadataMarker } from "performance-mark-metadata";
const marker = new PerformanceMetadataMarker();
const metadata = {
    details: { key: "value" }
};
const markerName = "name";
// mark with metadata
marker.mark(markerName, metadata);
performance.getEntriesByName(markerName).forEach(entry => {
    // get metadata for entry
    const result = marker.getEntryMetadata(entry);
    /*
    {
        details: { key: "value" }
    };
    */
    assert.strictEqual(result, metadata, "should get same metadata");
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 在 Node.js 中的用法

Node.js 8.5.0 介绍 [`perf_hooks`](https://nodejs.org/api/perf_hooks.html) 模块。
你可以通过将`require("perf_hooks").performance`传递给`PerformanceMetadataMarker`构造函数参数来使用这个库。

```
import { PerformanceMetadataMarker } from "performance-mark-metadata";
const nodePerformanceHook = require("perf_hooks");
const performance = nodePerformanceHook.performance;
const marker = new PerformanceMetadataMarker({
    performance
});
marker.mark("name", {
    details: { key: "value" }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 用例

这个例子展示了实际用法。

[![Example](img/9e7ccbcfb5abdbd2f485963c5fb28e35.png)T2】](https://azu.github.io/performance-mark-metadata)

*   参见[https://azu.github.io/performance-mark-metadata](https://azu.github.io/performance-mark-metadata)

您希望在查看站点时发现性能问题。
可以用`performance-mark-metadata`来分析问题。

对于[真实用户监控](https://en.wikipedia.org/wiki/Real_user_monitoring) (RUM)很有用。
在开发中，可以使用 browser 的开发工具，但是很难讲 RUM。

#### 标记点

*   每秒标记当前帧(FPS)
*   将每个动作标记为“onClick”

记录 FPS

```
const { PerformanceMetadataMarker } = require("performance-mark-metadata");
const marker = new PerformanceMetadataMarker();

const FpsEmitter = require("fps-emitter");
const fps = new FpsEmitter();
fps.on("update", function(FPS) {
    // mark current FPS
    marker.mark("FPS", {
        details: {
            FPS: FPS
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

并记录动作

```
// heavy task
const heavyTaskButton = document.getElementById("js-button");
heavyTaskButton.addEventListener("click", () => {
    marker.mark("Heavy Action");

    // ... heavy task ...
}) 
```

Enter fullscreen mode Exit fullscreen mode

之后就可以得到 FPS 和动作日志了。

```
const logData = performance.getEntriesByType("mark").map(entry => {
    const meta = marker.getEntryMetadata(entry);
    return {
        type: entry.name,
        timeStamp: entry.startTime,
        meta: meta
    };
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 分析学

您可以获取日志数据并分析日志数据。

例如，使用 [C3.js](http://c3js.org/) 可视化日志数据。

[![example.gif](img/1112536199d96a99a03d3d09c946304f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aL112Uue--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.rawgit.com/azu/performance-mark-metadata/f79fe225/docs/example.gif)

你可以发现“FPS”和“繁重任务”之间的关系。
因此，你可以发现性能问题是由“繁重的任务”引起的。

## 储存库

*   示例源代码:[性能-标记-元数据/文档/](https://github.com/azu/performance-mark-metadata/tree/master/docs)
*   [azu/performance-mark-metadata:`performance.mark`带自定义元数据。](https://github.com/azu/performance-mark-metadata)

欢迎来到⭐️和拉请求！