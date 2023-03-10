# 使用带要求的信号

> 原文：<https://dev.to/cwardzala/using-signalr-with-requirejs-29a6>

如果你不知道信号和要求

*   https://github . com/signal/signal
*   [http://requirejs.org](http://requirejs.org)

当我用 SignalR 和 RequireJS 构建我的应用程序时，我需要两个步骤才能工作。

## 1。设置相关性

我需要包装我所有的 SignalR javascript，以便它们能够正确地与 RequireJS 一起工作。

```
require.config({
    paths: {
        jquery: 'jquery-1.8.2.min'
    },
    shim : {
        "jquery.signalR-0.5.3": ['jquery'],
        "/signalr2/hubs": ['jquery', 'jquery.signalR-0.5.3'],
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用 RequireJS 2 强大的 shim 特性。我可以在 RequireJS 中使用非 AMD 兼容的脚本，而不用手动将它们包装在 define()语句中。我还为 jquery 设置了路径，因为它是一个命名模块，并且是 SignalR 脚本所需要的。

## 2。fix SignalR $ . connection . hub . start()

我从一个$(文档)中调用 SignalR 的$.connection.hub.start()。就绪()。甚至在我开始使用 RequireJS 加载我的脚本之前，这就很好地工作了。然而，在移动到 RequireJS 之后，这就停止工作了。为什么？基本上，SignalR 会默认延迟启动连接，直到页面完全加载完毕。在$.connection.hub.start()上输入`waitForPageLoad`选项。这告诉 SignalR 你是否想在开始连接之前等待页面加载，在我的例子中我不想。只需将该选项设置为`false`，它将在调用时启动连接，而不是等待。这解决了我的问题，SignalR 和 RequireJS 开始友好相处。注意:我还在 start()上使用回调来确保在使用我的服务器方法之前已经建立了连接。

```
$(document).ready(function () {
    $.connection.hub.start({waitForPageLoad:false},function () {
        // callback code
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode