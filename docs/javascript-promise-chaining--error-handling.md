# Javascript 承诺链和错误处理

> 原文：<https://dev.to/azizhk110/javascript-promise-chaining--error-handling>

以下可能看起来像一篇固执己见的文章，也请给出你的意见。事实上，我发表这篇文章是为了验证我的想法。如果你同意，请推荐。

## 改进一段糟糕的代码

所以让我们从糟糕的代码开始。这是一个没有逃脱回调地狱的人的例子。

```
function getHotDog () {
    return new Promise(function (resolve, reject) {
        getBun().then(function (bun) {
            addSausage(bun).then(function (bunWithSausage) {
                addSauce(bunWithSausage).then(function (hotdog) {
                    resolve(hotdog)
                })
            })
        })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

这里所有的底层函数都调用 return Promises，所以不需要创建 *new Promise()* 而是直接使用 return。

```
function getHotDog () {
    return getBun().then(function (bun) {
        return addSausage(bun).then(function (bunWithSausage) {
            return addSauce(bunWithSausage).then(function (hotdog) {
                return hotdog
            })
        })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，因为我们可以将一个函数的输出传递给另一个函数，我们可以像这样链接它们

```
function getHotDog () {
    return getBun().then(function (bun) {
        return addSausage(bun)
    }).then(function (bunWithSausage) {
        return addSauce(bunWithSausage)
    }).then(function (hotdog) {
        return hotdog
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在因为我们的函数很简单，我们甚至可以把它压缩到这个

```
function getHotDog () {
    return getBun()
    .then(addSausage)
    .then(addSauce)
} 
```

Enter fullscreen mode Exit fullscreen mode

简单易懂不是吗？

## 多个变量

现在让我们举一个需要两个变量的例子。例如，你有一种香肠，你需要一种特别的面包。在这个例子中，我们需要两个承诺的响应，其中一个依赖于另一个。

```
function getSausage () {
    return Promise.resolve({ // Can be an async func.
        type: 'soya',
        length: Math.ceil(Math.random()*10)
    })
}
function getBun (len) {
    return Promise.resolve({ // Can be an async func.
        type: 'wholewheat',
        length: len
    })
}
function addSausageToBun (bun, sausage) {
    return Promise.resolve({ // Can be an async func.
        bun: bun,
        sausage: sausage
    })
}

function getHotDog () {
    return getSausage().then(function (sausage) {
        return getBun(sausage.length).then(function (bun) {
            // Over here we need both variables sausage & bun,
            // So that is why we are creating a closure.
            // Where we have both.
            return anddSausageToBun(bun, sausage)
        })
    }).then(addSauce)
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以将内部函数提取到另一个函数中，但是你将在那里创建闭包，所以这是一回事。

还有一个使用 Promise.all 的选项，但我觉得这会使代码难以维护。但这是个人的选择。

```
function getHotDog () {
    return getSausage().then((sausage) => {
        return Promise.all([getBun(sausage.length), sausage]
    }).then(([bun, sausage] => {
        return anddSausageToBun(bun, sausage)
    }).then(addSauce)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 错误处理

现在我们知道了如何链接承诺，让我们看看一些错误处理。你能区分这两者吗？

```
function requestA(url) {
    return fetch(url).then(function (res) {
        return doSomething(res.data)
    }).catch(function (err) { // <- See this <<<<<<<<
        return fallbackForRequestFail()
    })
}

function requestB(url) {
    return fetch(url).then(function (res) {
        return doSomething(res.data)
    }, function (err) { // <- Change here. <<<<<<<<<<
        return fallbackForRequestFail()
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

上面两个例子之间有一个非常重要的区别，那些最近才从 jQuery 迁移到 React/Redux Stack 并开始使用 Axios、Fetch 或其他类似库的人会觉得 React 开始吞噬他们的错误，而不是向前传播它们。

> 我一看到就畏缩。然后(()= >调度(...)).接住(...)在 React 项目中。如果一个组件在分派过程中抛出，你就会陷入困境。
> 
> — Dan Abramov ([@dan_abramov](https://dev.to/dan_abramov) ) [August 31, 2016](https://twitter.com/dan_abramov/status/770914221638942720)

但这不是吃你的错误。

比方说，你在上面的`doSomething`函数中有一个运行时错误，在`requestA`流程中，你的错误将被捕获，然后`fallbackForRequestFail`将被调用，而实际上它应该是`fallbackForRuntimeError`或者应该被记录，这样你就会得到通知，而不是它就这样神秘地消失了。

```
function requestA(url) {
    return fetch(url).then(function (res) {
        // Gets called if request succeeds
        return doSomething(res.data)
    }).catch(function (err) {
        // Gets called if request fails, or even if .then fails
        return fallbackForRequestFail()
    })
}

function requestB(url) {
    return fetch(url).then(function (res) {
        // Gets called if request succeeds
        return doSomething(res.data)
    }, function (err) {
        // Gets called if request fails
        return fallbackForRequestFail()
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

所以本质上在`requestB`代码流中，只有函数会在两者之间被调用。任何其他错误(如运行时错误)都将提前传播，您可以单独处理这些错误，也可以将这些错误记录到您的错误记录服务中，并根据具体情况进行修复。

## 记录未处理的拒绝承诺

### 浏览器中的

```
window.addEventListener('unhandledrejection', event => {
    // Can prevent error output on the console:
    event.preventDefault();

    // Send error to log server
    log('Reason: ' + event.reason);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### Node.js 中的

```
process.on('unhandledRejection', (reason) => {
    console.log('Reason: ' + reason);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里阅读更多关于记录未处理拒绝承诺的信息:[http://www.2ality.com/2016/04/unhandled-rejections.html](http://www.2ality.com/2016/04/unhandled-rejections.html)

*本文由[媒体](https://medium.com/@azizhk/javascript-promises-best-practices-anti-patterns-b32309f65551)T3 交叉发布*