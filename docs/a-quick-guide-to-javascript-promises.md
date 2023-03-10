# JavaScript 承诺快速指南

> 原文：<https://dev.to/dkundel/a-quick-guide-to-javascript-promises>

当你编写 JavaScript 时，[回调](https://developer.mozilla.org/en-US/docs/Mozilla/js-ctypes/Using_js-ctypes/Declaring_and_Using_Callbacks#Using_Callbacks)是最容易混淆的概念之一。[承诺](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)是改进异步代码工作的新方法。

[![Decorative GIF](img/108da78b2ce75aaaf21a3dad4e223602.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dNE4hnVT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/31orCejQRkSvmchYeZC2GKswNtst-d_xEoSPoP3X-bAm9RRe8hxz59vVZrrRm78VvJgVbuUo5R7dAikR2gY1rxtqQ14yMJP8K4CS3Siiir_wRpB6IYgoWGlpokE51vV4eYAI2lpP-1.png)

回调的最大问题之一是不同异步活动的链接。您最终会一个接一个地调用匿名函数来传递值。结果是一个不可维护的“回调地狱”。承诺试图解决这个问题，但在开始时可能会有点混乱。

让我们来定义什么是承诺，为什么它们非常有用，以及如何并行或串行地执行调用。为此，我们将查看使用两个不同的 Node.js 库执行 HTTP 请求的不同示例。

## 设置

在我们开始之前，您需要以下东西来尝试我们的代码示例:

*   [node . js](https://nodejs.org/en/)6.0 以上版本。您可以通过在命令行中运行`node -v`来检查您的版本。你可以从他们的网站下载最新的二进制文件，或者使用类似于 [nvm](https://github.com/creationix/nvm) 的工具进行升级。

一旦你有了这个，你需要创建一个新的文件夹。我将在我的主目录中创建一个`promises`文件夹。在文件夹中运行以下命令安装 [`fetch`](https://www.npmjs.com/package/node-fetch) 和 [`request`](https://www.npmjs.com/package/request) 库:

```
npm install node-fetch request --save 
```

Enter fullscreen mode Exit fullscreen mode

创建一个名为`promises.js`的新文件，并在其中放置以下代码行来加载库:

```
const fetch = require('node-fetch');
const request = require('request'); 
```

Enter fullscreen mode Exit fullscreen mode

我们将在整篇文章中使用同一个`promise.js`文件。

## 快速承诺概述

为了理解承诺的好处，让我们首先看看如何在没有承诺的情况下进行异步调用。为此，我们将使用`request`库发出一个 HTTP GET 请求。

在`promises.js`中添加以下几行:

```
request.get('http://httpbin.org/get', (error, response, body) => {
  if (error) {
    console.error('Oh shoot. Something went wrong:');
    console.error(error.message);
    return;
  }

  console.log('Request done. Response status code: %d', response.statusCode);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过执行以下命令来运行这段代码:

```
node promises.js 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将回调函数作为第二个参数传递给`request.get` call。当 HTTP 请求的响应到来时，库将自动执行这个函数。它将传递三个参数。第一个参数是潜在的错误或`null`(如果成功的话)。第二个参数是 HTTP 响应，第三个参数是响应体。

如果我们使用`fetch`而不是`request.get`，我们可以利用承诺，因为 fetch 将返回一个`Promise`，而不是接受回调作为第二个参数。一个`Promise`是一个有两个重要方法的对象:`then()`和`catch()`。`then()`可以接收 1 或 2 个参数，而`catch()`可以用来处理错误。

对于`then()`，如果调用结果成功，则调用第一个函数参数。如果解析承诺时出现错误，将调用第二个函数。稍后我们将研究这个错误处理程序和`catch()`之间的区别。

用下面的代码替换前面的代码，开始使用 Promises:

```
fetch('http://httpbin.org/get')
.then(response => {
  console.log('Request using Promises done. Response status code: %d', response.status);
})
.catch(error => {
  console.error('Oh shoot. Something went wrong with the promise code:');
  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过再次执行`node promises.js`重新运行代码。

到目前为止，除了稍微干净一点之外，与回调代码没有太大的区别。当我们想要进行一些数据操作或进行多次调用时，真正的魔力就来了。为此，一般规则是，如果我们传递给`then`或`catch`的处理函数返回一个值或另一个承诺，承诺链将继续。

例如，添加一个提取状态代码并返回它的函数:

```
function extractStatusCode(response) {
  return response.status;
}

fetch('http://httpbin.org/get')
.then(extractStatusCode)
.then(statusCode => {
  console.log('Request using Promises, part II. Response status code: %s', statusCode);
})
.catch(error => {
  console.error('Oh shoot. Something went wrong with the promise code:');
  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

再次运行代码。控制台中的输出应该是相同的，但是我们的代码更加结构化。

这段代码将首先执行 HTTP 请求，然后调用`extractStatusCode`函数，一旦该函数返回，它将执行我们的匿名函数，该函数将记录响应状态代码。

## 捕捉错误

[![Decorative: GIF of little girl failing to catch a ball](img/bc98f7bcd6f79a93cf4665a1564df1c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ySV9ujWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/YS8PLVpcyhjaXzBZwzJ93O93W5blxqXQg-qqTIFEBXG0EG-OLRlgERwItSphhZ9imcpJQ35f0I7-s2FAZcQUHktJjU3-iMOTYuK2eFTQcctukZoxW9TzIvNAtrTNnR5YVtd7S5tX-4.png)

现在我们使用承诺，我们可能会遇到问题。如果我们不能正确地捕捉错误，我们所有的代码都会无声地失败。

想象一下使用承诺，比如将您的整个代码包装到 try {}块中。除非您显式地捕捉它们，否则您的代码将会悄悄地失败。捕捉错误非常重要，不仅仅是“普通的礼貌”。

为了正确地捕捉错误，我们有两个选择。第一种方法是将第二个函数传递到我们的 then()调用中。

对您的代码进行以下更改来测试这一点:

```
function extractStatusCode(response) {
  return response.status;
}

fetch('invalid URL')
.then(extractStatusCode, errorInFetch => {
  console.error('An error occurred in the fetch call.');
  console.error(errorInFetch.message);
  // return null as response code since no request has been performed
  return null;
})
.then(statusCode => {
  console.log('Request using Promises. Response status code: %s', statusCode);
})
.catch(error => {
  console.error('This will never be executed');
}); 
```

Enter fullscreen mode Exit fullscreen mode

当您运行这段代码时，您会看到它将命中我们添加的错误处理程序，并将相应的消息打印到屏幕上:

[![Screenshot of Terminal with error message](img/f0d0062a72cf8a309565197c04f6d7c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wTCJfPA6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/MDMEk6tcP9UztGgMMeYhTJVy9vSDPNxF-TE5gkr5HHGeoFimdsz1v985Uu0kAVwqQvzdRE25IXavzCaSJKhJqi5iDV0OX0I6kipDWE8vvOMlwbnbMC_0f2sGdTIGTWN42qRWG4ek-4.png)

然而，它没有执行`catch`处理程序，因为我们在处理程序中返回了一个值`null`。从那一点开始，承诺链被认为又回到了快乐的道路上，因为错误已经被处理了。

我们可以通过`throw`处理错误或者使用`Promise.reject(error)` :
返回一个新的承诺来确保它继续将此视为错误

```
function extractStatusCode(response) {
  return response.status;
}

fetch('invalid URL')
.then(extractStatusCode, errorInFetch => {
  console.error('An error occurred in the fetch call.');
  console.error(errorInFetch.message);
  // forward the error
  return Promise.reject(errorInFetch);
})
.then(statusCode => {
  console.log('Request using Promises. Response status code: %s', statusCode);
})
.catch(error => {
  console.error('This will now be executed as another exception handler.');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了如何用`then()`处理错误，这和`catch()`有什么区别？

为了理解这一点，让我们再次修改我们的`fetch`片段，使用一个有效的 url，在访问`status`属性:
之前，通过用`undefined`覆盖`response`来中断`extractStatusCode`函数

```
function extractStatusCode(response) {
  response = undefined;
  return response.status;
}

fetch('http://httpbin.org/get')
.then(extractStatusCode, errorInFetch => {
  console.error('This will not be executed.');
  console.error(errorInFetch.message);
  // forward the error
  return Promise.reject(errorInFetch);
})
.then(statusCode => {
  console.log('Request using Promises. Response status code: %s', statusCode);
})
.catch(error => {
  console.error('There was an error somewhere in the chain.');
  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/94ffc2f227d3690f7d634929c6a35d32.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xeyPI-c1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/6i33oKjzuVOypuMfZrOrLoDwwpQZNnqg1Bj6A96ggBi1f1jykx51RNVvHUoZity2m8ssF7dTdA0GHF8lkZVXnfJUiK0BPOnLwTT3ywctJhZpkJ_-BrYyOnDbUi_KJjy1OE4A8xOT-4.png)

不执行`then()`部分中的错误处理程序，因为该处理程序仅用于之前的承诺，而不是处理程序。然而，我们的`catch()`处理程序将被执行，因为它捕获了链中发生的任何错误。

## 并行执行

[![Decorative GIF: hacking scene with two people coding on one keyboard](img/6d4400c0abfdf0b8d2163d79b24bb808.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4XIocOsm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/gkYYQjOd3_hIFskG7pg6MBnP8uvvZFAL2j7xUHL-nPB7OvM835olCSo0594rCSnmvMl63Zyms_vZqHz1aKzPmY4wN6FpnQEhVYy-jWUw68YuPPrz6ieaW4VPxAXdYGWza_TdZBsc-4.png)

这就是承诺的魔力所在。考虑这样一种情况，我们想发送多个 HTTP 请求或进行多个异步调用，并想知道它们何时完成。

我们想要请求的端点保存在一个数组中。使用回调这可能会很混乱。为了完成它，我们必须在回调中使用计数器来检查我们是否完成了和其他类似的攻击。

有了承诺，我们可以简单地[将](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)映射到消息数组上，在 map 函数中返回承诺，并将结果数组传递给内置函数`Promise.all()`。这将返回一个新的承诺，当所有调用成功时，该承诺立即解决，或者当其中一个调用失败时，该承诺拒绝。

```
const queryParameters = ['ahoy', 'hello', 'hallo'];

const fetchPromises = queryParameters.map(queryParam => {
Â  return fetch(`http://httpbin.org/get?${queryParam}`)
Â  Â  .then(response => {
Â  Â  Â  // parse response body as JSON
Â  Â  Â  return response.json()
Â  Â  })
Â  Â  .then(response => {
Â  Â  Â  // extract the URL property from the response object
Â  Â  Â  let url = response.url;
Â  Â  Â  console.log('Response from: %s', url);
Â  Â  Â  return url;
Â  Â  });
});

Promise.all(fetchPromises).then(allUrls => {
Â  console.log('The return values of all requests are passed as an array:');
Â  console.log(allUrls);
}).catch(error => {
Â  console.error('A call failed:');
Â  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这段代码，您应该会收到多个请求。但是，由于这些调用是并行执行的，因此无法保证它们的运行和结束顺序。

## 连续执行

[![Decorative GIF: People standing in a queue bumping into each other](img/a97b5386a8728e92ff81c53bd2aa47b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fVa254Ep--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2016/09/5vTdWZEcbtgpgO-Wg4a7P_BPjeCG0DWjeK4uMsPlUvYgoAx0fASq-NJHwN9n_hjtkzly4zc2D0d3h-Az7PwrN1CiQm0DUCsII2j38mXtewOoYCKMaWGnNjsOPpIIGaiPWbhd4QRR-1.png)

虽然并行执行很酷，性能也很好，但由于限制或依赖性，我们有时不得不连续进行几次调用。我们也可以用承诺来解决这个问题。

当你知道所有必要的电话时，连锁承诺是非常容易做到的。然而，如果我们动态地生成我们需要执行的异步函数，那就更复杂了。

我们有一种方法可以做到这一点:

```
const queryParameters = ['ahoy', 'hello', 'hallo'];

let mostRecentPromise = Promise.resolve([]); // start with an immediately resolving promise and an empty list
queryParameters.forEach(queryParam => {
Â  // chain the promise to the previous one
Â  mostRecentPromise = mostRecentPromise.then(requestedUrlsSoFar => {
Â  Â  return fetch(`http://httpbin.org/get?${queryParam}`)
Â  Â  Â  .then(response => {
Â  Â  Â  Â  // parse response body as JSON
Â  Â  Â  Â  return response.json()
Â  Â  Â  })
Â  Â  Â  .then(response => {
Â  Â  Â  Â  // extract the URL property from the response object
Â  Â  Â  Â  let url = response.url;
Â  Â  Â  Â  console.log('Response from: %s', url);
Â  Â  Â  Â  requestedUrlsSoFar.push(url);
Â  Â  Â  Â  return requestedUrlsSoFar;
Â  Â  Â  });
Â  });
});

mostRecentPromise.then(allUrls => {
Â  console.log('The return values of all requests are passed as an array:');
Â  console.log(allUrls);
}).catch(error => {
Â  console.error('A call failed:');
Â  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的概念是将调用链接起来，一旦前一个调用解决了，就通过将它封装到一个`then()`处理程序中来执行下一个调用。如果我们知道调用的数量，这与我们手动执行的方法相同。

现在我们用一个 [`forEach`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 循环来实现这个。这是可行的，但它并不是最可读的解决方案。为了改善这一点，我们可以使用数组的 [`reduce`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 方法。

相应修改代码:

```
const queryParameters = ['ahoy', 'hello', 'hallo'];

let mostRecentPromise = queryParameters.reduce((previousPromise, queryParam) => {
Â  return previousPromise.then(requestedUrlsSoFar => {
Â  Â  return fetch(`http://httpbin.org/get?${queryParam}`)
Â  Â  Â  .then(response => {
Â  Â  Â  Â  // parse response body as JSON
Â  Â  Â  Â  return response.json()
Â  Â  Â  })
Â  Â  Â  .then(response => {
Â  Â  Â  Â  // extract the URL property from the response object
Â  Â  Â  Â  let url = response.url;
Â  Â  Â  Â  console.log('Response from: %s', url);
Â  Â  Â  Â  requestedUrlsSoFar.push(url);
Â  Â  Â  Â  return requestedUrlsSoFar;
Â  Â  Â  });
Â  });
}, Promise.resolve([]));

mostRecentPromise.then(allUrls => {
Â  console.log('The return values of all requests are passed as an array:');
Â  console.log(allUrls);
}).catch(error => {
Â  console.error('A call failed:');
Â  console.error(error.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的总体方法与`forEach`循环相同。我们指定一个起始值`Promise.resolve([])`，并使用一个接收两个参数的函数调用`messages`数组上的`reduce`方法。一个是先前的返回值，另一个是我们正在访问的数组的当前值。这样我们就可以将`reduce`数组转换为单个值。在我们的情况下，这将是最近的承诺，然后我们可以使用它来知道什么时候一切都完成了。

## 把回拨码变成承诺

现在我们知道如何使用承诺，我们有一个问题要解决。我们如何处理不支持承诺的异步代码？为此，我们可以将该函数包装成一个新函数，并使用`new Promise()`构造函数。这个构造函数接收一个带有两个参数的函数:`resolve`和`reject`。当我们想要解决或拒绝一个承诺时，这些参数就是我们调用的函数。

下面是一个示例函数，它从磁盘中读取一个文件，并在 Promise 中返回内容:

```
const fs = require('fs');

function readFileWithPromise(path) {
Â  return new Promise((resolve, reject) => {
Â  Â  fs.readFile(path, 'utf8', (err, content) => {
Â  Â  Â  if (err) {
Â  Â  Â  Â  return reject(err);
Â  Â  Â  }
Â  Â  Â  return resolve(content);
Â  Â  });
Â  });
}

readFileWithPromise('/etc/hosts').then(content => {
Â  console.log('File content:');
Â  console.log(content);
}).catch(err => {
Â  console.error('An error occurred reading this file.');
Â  console.error(err.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当我们用一个函数作为参数调用`new Promise()`时，这个函数将立即被异步执行。然后我们用必要的参数执行`fs.readFile`。一旦执行了`readFile`调用的回调，我们就检查是否有错误。如果有错误，我们将`reject`承诺相应的错误。如果没有错误我们`resolve`该答应。

最近 Node.js 还在内置的`util`模块中引入了一个名为 [`promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) 的新函数。该函数允许您轻松地将内置的基于回调的函数转换为基于承诺的函数。[查看文档了解更多信息](https://nodejs.org/api/util.html#util_util_promisify_original)。

## 结论

现在你有希望对承诺有一个更好的想法，并准备抛弃旧的*回调地狱*的时代，获得一些更干净和更易维护的代码。如果你已经迷上了，你应该看看 JavaScript 的未来会给 [async/await](https://www.twilio.com/blog/2015/10/asyncawait-the-hero-javascript-deserved.html) 带来什么，以进一步改进 JavaScript 中的异步编程。

另外，一定要让我知道你对 Promises 的体验，以及你为什么喜欢(或讨厌)它。也许你甚至有一个疯狂的黑客使用承诺，你想炫耀？给我写封信:

*   电子邮件:[dkundel@twilio.com](mailto:dkundel@twilio.com)
*   推特: [@dkundel](https://twitter.com/dkundel)
*   GitHub: [dkundel](https://github.com/dkundel)

* * *

*JavaScript 承诺快速指南最初发表于 2016 年 10 月 3 日 [Twilio 博客](https://www.twilio.com/blog)。*