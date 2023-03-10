# ES6 兼容睡眠功能

> 原文：<https://dev.to/adamkdean/es6-compatible-sleep-function-3e18>

在本地调试时，很难看出应用程序在野外是如何运行的，因为网络没有延迟。通常，你可以使用睡眠功能来解决这个问题。

在 ES6 之前的生成器中，您可以通过回调来实现这一点，可能使用调用回调的 setTimeout。

```
function sleep(ms, callback) {
    setTimeout(callback, ms);
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 ES6 生成器，在你想要`yield sleep(1000)`等的地方，你不能使用回调。你可以做的是返回一个带单个参数的函数，`done`，这个函数通过闭包可以访问你想要传入的参数，`ms`。当返回的函数被调用返回函数的 cog 调用时，您的内部函数将可以访问您传入的`ms`参数，以及 JS 传入的回调，当被调用时，将从您放弃睡眠的地方继续。

```
function sleep(ms) {
    return function(done) {
        setTimeout(done, ms);
    }
}

yield sleep(1000); 
```

Enter fullscreen mode Exit fullscreen mode

这个现在在 npmjs.org 上可以买到。要安装:

```
npm install es6-sleep 
```

Enter fullscreen mode Exit fullscreen mode

为了使用，让我们说，而在一些 Koa 中间件:

```
var sleep = require('es6-sleep');

app.use(function *() {
    // do something
    yield sleep(1000);
    // continue
}); 
```

Enter fullscreen mode Exit fullscreen mode

希望这有意义。在我脑子里是的。