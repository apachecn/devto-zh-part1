# 对 jQuery 的$使用异步 await。创建交互式、快速动态网页应用的网页开发技术

> 原文：<https://dev.to/ptasker/using-async-await-with-jquerys-ajax-ba5>

<small>照片由[丹尼斯·内沃扎伊](https://unsplash.com/photos/2vmT5_FeMck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄</small>

如果您和我一样，您可能会经常使用 jQuery。它无处不在，说实话，它是一个坚实、成熟的库。它通常已经加载在页面上，尤其是当你使用 WordPress 的时候。

除了 DOM 操作(你现在可以用原生 JS 做*大部分*)，jQuery 的`$.ajax()`方法真的很方便而且工作得很好。

但是你知道这个函数提供了开箱即用的 Promise 接口吗？我最近想起这件事，心想:

> 嗯，我想知道我是否可以在 jQuery 的$中使用 async/await。ajax()。

事实证明，你可以！

## 设置

Async/await 仍然很新，它只出现在 [ES2017 规范](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)中，所以你需要使用 [transpiler](https://scotch.io/tutorials/javascript-transpilers-what-they-are-why-we-need-them) 像 [Babel](https://babeljs.io/) 来让它在旧浏览器中工作。无论如何，我们大多数人都在用 Babel 来打包( [Webpack](https://babeljs.io/docs/setup/#installation) ，Browserify)，所以这没什么大不了的。

假设你已经安装并配置了 Babel，你需要做的第一件事就是让 Babel 使用‘env’预置。在你的。babelrc 文件，添加这几行:

```
{
...
"presets": ["babel-preset-env"],
...
} 
```

Enter fullscreen mode Exit fullscreen mode

你还必须从 npm: `npm i -D babel-preset-env babel-polyfill`安装这个 Babel 预置和 polyfill。

一旦完成，你还需要安装这个[魔法插件](https://babeljs.io/docs/plugins/transform-async-to-generator/)给巴别塔:`npm i -D babel-plugin-transform-async-to-generator`。这是允许您在代码中使用 async/await 的关键包。我应该提到，这只是让 Babel 将 async/await 语法编译到 ES2015 生成器，所以如果你不是针对大多数现代浏览器，请记住这一点。

下一步，也是最后一步你需要做的是在你的代码中使用`babel-polyfill`模块。如果你愿意，你可以使用一个 Webpack 加载器，或者只是在你的源文件中包含这个包:

```
import 'babel-polyfill'; 
```

Enter fullscreen mode Exit fullscreen mode

唷！

好了，现在我们准备好出发了。启动 Webpack，让我们开始使用 async/await！

## 不要叫我，也许

以前你必须使用美元。ajax()这样:

```
//Function wrapper that confuses alot of devs because async code works differently
function doAjax() {
    $.ajax({
        url: ajaxurl,
        type: 'POST',
        data: {
            stuff: "here"
        },
        success: function (data) {
            //wacky nested anonymous callbacks go here
            var something_but_not_really = data;
        },
        error: function (jqXHR, textStatus, errorThrown) {
            // Empty most of the time...
        }
    });

    return something_but_not_really
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道当我还是一名初级开发人员时，我不知道为什么`something_but_not_really`是`undefined`。我不得不学习十亿次关于回调 [![😬](img/0f615ca8ced5e5acc0a9e5ab9485db7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i0aoqRIO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/2.3/72x72/1f62c.png) 。

但是现在…

```
async function doAjax(args) {

    const result = await $.ajax({
        url: ajaxurl,
        type: 'POST',
        data: args
    });

    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

而结果*实际上*返回的是 AJAX 结果。酷吧？

async/await 的一大好处是它使异步代码*看起来*是同步的。比如，做这件事，等它完成，然后给我结果。

## 错误

注意到我们的新函数中缺少了什么吗？是的，错误处理是不存在的。幸运的是，由于 async/await 本质上是同步的，所以可以使用`try...catch()`！！！

```
async function doAjax(args) {
    let result;

    try {
        result = await $.ajax({
            url: ajaxurl,
            type: 'POST',
            data: args
        });

        return result;
    } catch (error) {
        console.error(error);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。捕捉内置时出现错误。现在，使用 async/await 还有其他方法来处理错误，但是它们比稍微复杂一点。

现在要记住的另一件事是，因为我们正在返回一个等待函数的结果，`result`将等于一个[承诺实例](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。你有两个选择来处理结果。

第一个选项是确保稍后调用`doAjax()`时使用 await。

```
// Elsewhere in code, inside an async function
const stuff = await doAjax(); 
```

Enter fullscreen mode Exit fullscreen mode

另一个选择是使用`Promise`界面，并以这种方式滚动:

```
doAjax().then( (data) => doStuff(data) ) 
```

Enter fullscreen mode Exit fullscreen mode

承诺并不全是坏事，看起来更干净或者更容易处理，这取决于。我发现使用 ES2015 类有时更容易使用 Promise 接口，所以 YMMV。

但就是这样——今天就去使用 async/await 获得您的`$.ajax()`!

post [使用 jQuery 的 async await。ajax](http://petetasker.com/using-async-await-jquerys-ajax/?utm_source=devto) 最早出现在[上🔥数据库关键🔥](http://petetasker.com?utm_source=devto)。