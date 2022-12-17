# 检测 JS 是否在节点下运行

> 原文：<https://dev.to/adamkdean/detect-if-js-is-running-under-node-ahi>

以下是如何检测 JavaScript 是否在节点下运行的代码片段:

```
var isNode = typeof process !== "undefined" && 
    {}.toString.call(process) === "[object process]"; 
```

我们在这里检查变量`process`是否被定义，如果是，我们检查它的类型以确保它是正确的流程对象，而不仅仅是一个普通的旧 JavaScript 对象。