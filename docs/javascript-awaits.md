# JavaScript 正在等待

> 原文：<https://dev.to/kayis/javascript-awaits>

在 ES2017 中，JavaScript 获得了一个名为[异步函数](http://2ality.com/2016/02/async-functions.html)的特性。它们是一个方便的特性，可以进一步简化您的异步代码。

“但是，凯，我刚刚才明白，承诺才是正道！我所有的一元挣扎都是徒劳的吗？!"

你很幸运，因为异步函数基本上是[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)的语法糖。

# 为什么？

就像所有的语法糖一样，它们清理你的代码。他们隐藏了一点复杂性，但你必须看到自己是否值得。

例如，承诺链可能是这样的:

```
function f() {
  getServerData()
  .then(parseData)
  .then(filterData)
  .then(relayData)
  .catch(handleError);
} 
```

Enter fullscreen mode Exit fullscreen mode

用一个异步函数来写它，它可以是这样的:

```
async function f() {
  try {
    const dataString = await getServerData();
    const parsedData = await parseData(dataString);
    const filteredData = await filterData(parsedData);
    await relayData(filteredData);
  }
  catch(e) {
    handleError(e);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

凯，你疯了吗？！你说它会清理我的代码没问题，但看看你把它搞得多糟糕！”

是的，你是对的，特别是如果你来自一个函数式编程背景，这看起来一定是完全疯狂的。这可能不是最好的例子，但它表明了一件事:错误处理就像许多开发人员习惯的那样，只是试着捕捉并完成。这是因为异步函数支持同步和异步代码的混合。

这里的另一件事是，*等待的*函数现在简单地返回它们的值，所以你不必再浪费时间，你可以简单地编写你的异步代码，就像它是同步的一样。这使您能够在其他同步结构中使用它，比如循环或 if 语句。

```
async function f() {
  if (await isLoggedIn()) g()
  else h()
}

async function i() {
  const parsedElements = []
  while(let x = await getNextElement()) {
    let y
    try {
      y = await parse(x);
    }
    catch(e) {
      y = handleParseError(e);
    }
    parsedElements.push(y)
  }
  return parsedElements;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，同步和异步代码现在在一个函数中很好地结合在一起，因为它只是承诺，所以您可以在开箱即用的基于承诺的函数中使用它。

```
function addOne(x) {
  return Promise.resolve(x + 1);
}

async function g() {
  const two = await addOne(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

反之亦然，如果你有一个异步函数，你可以把它作为一个基于承诺的函数来使用，在其他地方也确实如此。因此，如果你用异步函数编写了所有的代码，而其他人想要使用它，他们不会被迫使用这个特性。

```
async function f() {
  let data
  try {
    data = await parseData(await getData());
  }
  catch(e) {
    data = handleError(e);
  }
  return data
}

function g() {
  f().then(handleNewData);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如何？

要使用此功能，您需要

*   像[巴别塔](https://babeljs.io/)这样的编译器，带有 [ES2017 预置](http://babeljs.io/docs/plugins/preset-es2017/)
*   Node.js >7.6.0
*   当前浏览器的版本

[目前](https://github.com/tc39/ecmascript-asyncawait/issues/9#issuecomment-127427447)`await`关键字只在异步函数中可用，所以你不能在你的 JavaScript 文件的全局范围内使用它，你必须定义一个函数为`async`。

这有一点限制，但是正如我所说的，**异步函数只是普通的函数，只是返回一个承诺，而不是它们的*真实值*T3。所以你可以把它们和任何框架或库一起使用，要么希望你给它一个承诺或承诺返回函数，要么对返回值不做任何事情，这是许多需要简单回调的函数的情况。** 

```
const fs = require("fs");
fs.readFile('README.md', async function (e, data) {
  if (e) return console.error(e);
  if (await validateOnServer(data)) console.log("File OK");
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

如果你喜欢命令式编程，我认为异步函数是集成同步和异步代码的好方法。如果你已经理解了承诺，你应该感到很自在。

对于函数式编程来说，隐藏承诺可能是一种倒退，但是函数式程序员很可能在几年前就把承诺留给了可观察的事物。