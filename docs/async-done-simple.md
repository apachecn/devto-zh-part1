# 异步完成简单

> 原文：<https://dev.to/petarprok/async-done-simple>

Javascript 是单核编程语言。如果您想与 HTML DOM(文档对象模型)交互，或者您正在做小项目，这可能是件好事。在其他情况下，如果你正在使用 mean stack(MongoDB，express，angularjs，node.js)或其他系统，你可能会使用异步功能。

其工作原理是:

最好的理解方式是通过例子。假设你在一家咖啡店，想要一杯黑咖啡。你从玛丽亚那里点了咖啡，她准备好了给你打电话，你就可以得到你的咖啡了。你对咖啡的渴望是一个新的异步呼叫，当玛丽亚说等待咖啡是一个承诺。玛丽亚打电话给你说你的订单已经准备好了。如果有任何问题，玛丽亚会告诉你，那就是拒绝。

如果你使用没有任何包的“普通”javascript async，它看起来像这样:

```
function myAsyncFunction(url) {
    return new Promise((resolve, reject) => {
        Your code goes here
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，我们都应该这样做！

不要！

原因如下:

对我个人来说，将所有代码放在另一个函数中不利于阅读代码，它会变得混乱，难以阅读。如果可以，我永远不会把我的代码放在另一个函数中(回调函数除外)。

它适用于小函数，不需要很多行，而且很简单。

然后我对自己思考如何改进它，使它面向对象和简单。一个半星期后，我有了这个解决方案:

```
exports.simpleAsync = function () {
    this.resolve;
    this.reject;
    this.promise = new Promise((resolve, reject) => {
        this.resolve = resolve;
        this.reject = reject;
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

它简单、干净、轻便且易于使用。它可以像老式的 q pacakge 一样用于异步。

您应该如何使用它:

```
var Async = require('simplify-async').simpleAsync;

    function test() {
        var promise = new Async();

        setTimeout(() => {
        promise.resolve("Have nice day");
        }, 5000);

        return promise.promise;
    }

test().then((response) => {
    console.log(response)   
}).catch((err) => {
    console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下发生了什么:

测试是异步函数。第一件事是你必须用 require 函数创建你导入的异步包的新实例。下一步是你必须返回 promise，因为如果你不这样做，js 将返回类似这样的错误:无法读取属性。然后是未定义的。当你得到数据后，你需要做的就是调用 promise.resolve 函数，异步调用就完成了。

我希望这个小巧、超级简单的异步包能帮助您做更多的事情！

如果你想检查我的包，它在 github 上:

[https://github.com/PetarProkopenko/simple-async](https://github.com/PetarProkopenko/simple-async)

npm 包的链接是:

[https://www.npmjs.com/package/simplify-async](https://www.npmjs.com/package/simplify-async)

注意:我这样做不仅仅是为了升职，我这样做是因为我热爱编程，我想为社区做贡献。这是我的第一篇文章和包裹，如果你有任何想法让我知道，或者如果我做错了什么...
感谢您抽出时间阅读，希望您度过愉快的一天。