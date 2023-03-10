# 让陈旧的回调适应闪亮的异步函数

> 原文：<https://dev.to/geoff/adapting-those-callbacks-to-async-functions-3ihg>

在为一个兼职项目编写代码时，我偶然发现了 Javascript/Node.js 中最近的`async` / `await`特性的一些用例，这促使我写了[这篇文章](https://dev.to/geoffdavis/writing-asyncawait-middleware-in-express-6i0)。

继续那个项目，我意识到我用来与数据库交互的库使用了回调函数；这并不是世界上最糟糕的模式，但是我编写了一个 API 包装类，发现从深层回调发送查询结果很笨拙。最终，它工作得很好，但我想要一个更优雅、更容易遵循的解决方案。

然后我开始思考“如果有一种方法可以包装一些回调函数，让我异步使用，同时保持代码干燥，那会怎么样”。

而从那点火花中， [**`asyncAdapter`**](https://github.com/geoffdavis92/asyncAdapter) 诞生了。

## 问题

假设您的函数创建了一个将响应数据传递给回调函数的`XMLHttpRequest`，例如:

```
function httpGET(endpoint, callback) {
  var xhr = new XMLHttpRequest();
  xhr.addEventListener("readystatechange", function() {
    if (this.readyState == 4 && this.status == 200) {
      callback(JSON.parse(xhr.responseText));
    }
  });
  xhr.open("GET", endpoint);
  xhr.send();
} 
```

Enter fullscreen mode Exit fullscreen mode

它一直是一个值得信赖的助手，但是它有点过时，并且使得使用检索到的数据比现代 Javascript 需要的更复杂。

你想使用 tc39 和 Babel 所能提供的最新最好的 API——比如`async/await`或`Promise`API——而回调就是不能满足它。

你能做什么？

## 我的解决方案

输入 **`asyncAdapter`** 。这个漂亮的实用程序 ***神奇地*** 将这个函数变成一个新的基于`Promise`的函数，允许它被`await`处理或者像承诺一样处理；这是通过传入 Promise 的`resolve`参数来实现的，其中包含了原始函数的回调。

(好吧，这并不完全是魔法，但仍然很酷)

下面是如何使用上面的示例函数和`asyncAdapter` :

```
const asyncHttpGET = asyncAdapter(httpGET, "https://example.com/api/data");

(async function someAsyncFunction() {
  const data = await asyncHttpGET;
  console.log(data); // -> { foo: 'bar' }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

适配器的第一个参数**是原始函数名，**其余的参数**以同样的顺序构成了传递给原始函数的任何参数。**

请注意，您应该**而不是**将回调参数位置中的一个函数传递给`asyncAdapter`参数，除非该函数可以返回值(例如，对于基于 AJAX/ `Promise`的函数来说不是这样)。

下面是一个与`asyncAdapter` :
一起使用的非异步函数的例子

```
// Original function
const add = (n1, n2, callback) => callback(n1 + n2);

// Add callback function to return value
const asyncSum20 = asyncAdapter(add, 10, 10, n => n);

// Add callback function to return value with side effects
const asyncSum50 = asyncAdapter(add, 10, 10, n => n + 30);

// Use inside function to create DRY async version of original function
const asyncSum = (n1, n2, n3) => asyncAdapter(add, n1, n2, n => n + n3);

(async function someAsyncFunction() {
  const sum20 = await asyncSum20;
  const sum50 = await asyncSum50;
  const sum100 = await asyncSum(5, 20, 75);

  console.log(sum20); // -> 20
  console.log(sum50); // -> 50
  console.log(sum100); // -> 100
}); 
```

Enter fullscreen mode Exit fullscreen mode

我发现这种实现相当灵活，并且在将适配器包装在函数中时提供了函数式编程的一些好处(就像上面的`asyncSum`函数)。

请注意，对于每个第三方基于回调的函数，这可能不是 100%开箱即用的；因为`asyncAdapter`依赖于回调参数是函数参数中的最后一个集合，所以这种模式对于那些可以将它应用于自己的代码库函数并控制这些函数的参数顺序的人来说可能更有价值。

## 结论

这听起来像是你能用的东西吗？或者是一个有趣的实用函数？

你很幸运！我刚刚在 npm 注册处[这里](https://www.npmjs.com/package/async-adapter)发布了这篇文章。

用您最喜欢的 npm 客户端安装它...

`npm i async-adapter`

`yarn add async-adapter`

找到一个 bug 或者对一个特性有一个想法？提交[问题](https://github.com/geoffdavis92/asyncAdapter/issues)或提交[拉动请求](https://github.com/geoffdavis92/asyncAdapter/pulls)。

我希望你喜欢这个包，并发现它很有用。感谢阅读！

### 进一步阅读

*   [异步功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
*   [等待关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)