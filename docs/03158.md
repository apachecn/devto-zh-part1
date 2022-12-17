# 用 Express 编写异步/等待中间件

> 原文：<https://dev.to/geoff/writing-asyncawait-middleware-in-express-6i0>

你在你的[快递](https://expressjs.com)应用中使用基于承诺的数据抓取吗？您是否厌倦了在`.then()`方法调用中嵌套路由处理代码语句？想以实用、适用的方式学习`async/await`？如果你的答案是“是的！”对于这些问题，请继续阅读，我将帮助您将这些承诺转化为异步[中间件](https://expressjs.com/en/guide/writing-middleware.html)功能。

(如果你只是想看看我的示例库和完成的代码，[请点击这里](https://github.com/geoffdavis92/express-async-await-middleware)

## 问题

如果您和我一样，您的快速路由器通过使用基于 [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的方法从各自的 API 端点或数据库请求数据来获取数据。这是一种现代的方法，可以缓解许多开发人员称之为“[回调地狱](http://callbackhell.com/)”的问题。

下面的片段是模仿我当前的一个 Express 副业项目；注意传递数据的异步的、基于承诺的结构，而不是同步的、基于函数的回调。

```
/* Example of a Promise-based data request approach */
const endpoint = 'https://jsonplaceholder.typicode.com/posts/1';
app.get('/',(req,res) => {
  PromiseBasedDataRequest(endpoint).then(data => {
    const { title, body } = data;
    req.render('post', { title, body });
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们使用相对平坦的承诺，而不是在回调函数之上使用金字塔形的回调函数，太好了！

但是一旦我们的数据通过了`.then()`调用链，我们现在必须编写一堆代码来处理数据和/或发送对路由器请求的实际响应。一些开发人员会以这种方式处理他们的代码，如果这对你有用，那就太好了；然而，现在 async/await 在 Node 中被支持，它不需要*有*来支持这种方式。

## 我的解决方案

从 7.6 版开始，Node.js 支持[异步函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)(通常称为`async/await`)，我们现在可以直接从`async`中间件函数中的解析`Promise`中提取数据，并以清晰易读的方式将数据传递给最终的路由器回调。

将下面的中间件函数视为对前面代码片段的更新:

```
const endpoint = 'https://jsonplaceholder.typicode.com/posts/1';
const asyncMiddleware = async (req,res,next) => {
  const data = await PromiseBasedDataRequest(endpoint);
  req.data = data.json()
  next()
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能注意到我们增加了一些东西:

1.  函数声明前面的 [`async`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 关键字；这向引擎表明，在函数体的某个地方，有一个对 Promise 的调用，并且 Promise 经常使用`await`关键字(见#2)。
2.  将 [`await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) 关键字在`PromiseBasedDataRequest`调用前赋值给`data`变量；这向引擎表明，所有其他代码的执行都应该暂停，直到承诺解决。(即应用程序“等待”请求的结果)这也允许将`PromiseValue`赋给一个变量并在以后使用。

之后，从`endpoint`中取出的`data`被赋给`req`/请求对象上的一个属性；这允许稍后通过`req.data`在应用程序中访问该值。最后，对`next()`的调用将`req`和`res`对象发送到下一个中间件，或者，当没有其他中间件时，发送到最终的路由回调。

让我们把我们的`asyncMiddleware`函数放到路由器的链中；在 Express 中，这发生在路由和最终回叫之间。(您可以在这里放置任意多的中间件函数，只是不要忘记在每个函数的末尾调用`next()`！)

```
app.get('/', asyncMiddleware, (req,res) => {
  const { title, body } = req.data;
  req.render('post', { title, body });
}) 
```

Enter fullscreen mode Exit fullscreen mode

哇！如此清新，如此干净。

现在，我们有了一个非常扁平和可读的路由器声明，我们的大部分代码通常位于路由器 callback^[【1】](#note1)中，而不是位于中间件函数中，数据被同步传递到最终的路由器回调函数中。

如果您想在一个“已完成”的项目中看到这种实现/模式，请查看我为本文设置的示例 repo 。按照自述文件中的说明开始；如果您发现有问题或需要帮助，请随时向[提交问题](https://github.com/geoffdavis92/express-async-await-middleware/issues/new)，我将很乐意与您一起审查。

## 动机

我研究这个解决方案的动机是因为我想学习和试验更多的`async` / `await`函数和 Express 中间件；我学习的最好方法是通过制作测试项目和拥有实现特性的实践经验。

我计划在一个利用 Javascript [Contentful SDK](https://github.com/contentful/contentful.js) 和 [MySQL 驱动程序](https://github.com/mysqljs/mysql)的副业项目中使用这种编写异步中间件的确切模式。我在这个项目中的当前实现与第一个代码片段示例完全一样:调用相应的 API，并将实际的`res` /response 调用封装在回调或`.then()`方法调用中。通过将这些特性重写到中间件功能中，我的目标是简化新路由器路径/API 端点的编写，并有望增加代码的可重用性。

## 结论

最后，利用`async` / `await`作为您的快速中间件实现有助于保持您的代码可重用、可读，并且与当前的编码约定保持一致。

希望我的小周六实验帮助你更好的理解异步函数，表达中间件。感谢阅读！

### 进一步阅读

*   [异步功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
*   [等待关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
*   [承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   [ExpressJS](https://expressjs.com)
*   [异步 Javascript 指南](https://medium.com/dev-bits/writing-neat-asynchronous-node-js-code-with-promises-32ed3a4fd098)

### 备注

[1]:根据[这个堆栈溢出帖子](https://stackoverflow.com/questions/20068467/do-never-resolved-promises-cause-memory-leak)，未解决的承诺不会造成任何严重的内存泄漏威胁。