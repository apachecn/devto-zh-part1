# 函数式 JavaScript:顺序解析承诺

> 原文：<https://dev.to/joelnet/functional-javascript-resolving-promises-sequentially-cne>

我喜欢 ES6 附带的新的 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 库，尽管有一样东西被遗漏了，一个顺序执行多个承诺的功能。

我们可以使用类似 [Q](https://github.com/kriskowal/q) 、[蓝鸟](https://github.com/petkaantonov/bluebird)、 [RSVP.js](https://github.com/tildeio/rsvp.js/) 、 [Async](https://github.com/caolan/async) 等库。或者我们可以自己做。我真的只需要一个函数，为一个函数导入整个库似乎有点沉重，这就是我创建这个的原因。

这个承诺的一个特点是它立即执行。这实际上对我们不利，当我们准备好执行时，我们需要承诺来执行。

我这样做的方法是将每个承诺转换成一个工厂函数。工厂函数将是一个返回承诺的简单函数。现在，当我们做出决定时，我们的承诺就会实现。

对于这个虚构的例子，我决定使用 jQuery 的 ajax 方法作为我的承诺。

```
// some dummy urls to resolve
const urls = ['/url1', '/url2', '/url3']

// convert each url to a function that returns an ajax call
const funcs = urls.map(url => () => $.ajax(url)) 
```

Enter fullscreen mode Exit fullscreen mode

解决这个问题有点复杂，我发现它有助于我提前考虑我们的函数应该输出什么。大概是这样的:

```
Promise.resolve()
  .then(x => funcs[0]()) // resolve func[0]
  .then(x => funcs[1]()) // resolve func[1]
  .then(x => funcs[2]()) // resolve func[2] 
```

Enter fullscreen mode Exit fullscreen mode

我还希望最终承诺返回一个包含每个承诺结果的数组。

这是最复杂的部分。我需要用一个空数组`[]`开始每个承诺，然后将每个承诺的结果连接到该数组。跟着我，我会尽力分解它。

我将用一个空数组的初始值开始这个承诺，就像这样`Promise.resolve([])`。然后使用 Promise 的`then`函数执行每个工厂函数。

为了简单起见，这个例子只解析 func 的索引`0`。剩下的我们以后再做。

```
// start our promise off with an empty array. this becomes all.
Promise.resolve([])
  // all is the array we will append each result to.
  .then(all => {
    return funcs[0]().then(result => {
      // concat the resolved promise result to all
      return all.concat(result)
    })
   }) 
```

Enter fullscreen mode Exit fullscreen mode

通过从我们的代码中删除所有的`{`、`}`和`return`，这段代码可以用一种更简洁的方式来表达。

```
Promise.resolve([])
  .then(all => funcs[0]().then(result => all.concat(result))) 
```

Enter fullscreen mode Exit fullscreen mode

去掉箭头函数一个巧妙的方法是像这样直接调用`concat`:

```
Promise.resolve([])
  .then(all => funcs[0]().then(Array.prototype.concat.bind(all))) 
```

Enter fullscreen mode Exit fullscreen mode

最后，这将是我们函数的输出:

```
Promise.resolve([])
  .then(x => funcs[0]().then(Array.prototype.concat.bind(x)))
  .then(x => funcs[1]().then(Array.prototype.concat.bind(x)))
  .then(x => funcs[2]().then(Array.prototype.concat.bind(x))) 
```

Enter fullscreen mode Exit fullscreen mode

还不算太糟吧？现在我们知道了我们的输入和输出，让我们开始吧！

我们可以使用 for 循环(但这不是很实用)，我们也可以使用递归，但我真正喜欢这个问题的是`reduce`。

> 每当需要将一个列表转换成一个对象时，可以考虑使用 reduce。

我们的`promiseSerial`函数应该接受一个工厂函数数组(每个函数返回一个承诺),并将它们简化为上面表示的单个承诺链。

我们的初始值`Promise.resolve([])`像这样传递给我们的 reduce 方法:

```
const promiseSerial = funcs =>
  funcs.reduce((promise, func) => ???, Promise.resolve([])) 
```

Enter fullscreen mode Exit fullscreen mode

最后一部分是从上面概括我们的一个承诺`then`，并更新一些参数名称。(新部分以粗体显示)

```
const promiseSerial = funcs =>
  funcs.reduce((promise, func) =>
    promise.then(result =>
      func().then(Array.prototype.concat.bind(result))),
      Promise.resolve([])) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！非常简单...划掉那个...将按顺序解析承诺的短函数。最后，让我们一起拍吧。

```
/*
 * promiseSerial resolves Promises sequentially.
 * @example
 * const urls = ['/url1', '/url2', '/url3']
 * const funcs = urls.map(url => () => $.ajax(url))
 *
 * promiseSerial(funcs)
 *   .then(console.log)
 *   .catch(console.error)
 */
const promiseSerial = funcs =>
  funcs.reduce((promise, func) =>
    promise.then(result => func().then(Array.prototype.concat.bind(result))),
    Promise.resolve([]))

// some url's to resolve
const urls = ['/url1', '/url2', '/url3']

// convert each url to a function that returns a promise
const funcs = urls.map(url => () => $.ajax(url))

// execute Promises in serial
promiseSerial(funcs)
  .then(console.log.bind(console))
  .catch(console.error.bind(console)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经消除了安装第三方库和全新`promiseSerial`功能的需要。

嘿！你居然坚持到了这篇文章的结尾！

你在顺序解决承诺的用例是什么？你是怎么解决的？请分享你的经历。

我知道这是一件小事，但当我在这里和 Twitter ( [@joelnet](https://twitter.com/joelnet) )上收到这些关注通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。

干杯！

更多文章@[https://medium.com/@joelthoms/latest](https://medium.com/@joelthoms/latest)

原贴@[https://hacker noon . com/functional-JavaScript-resolving-promises-sequentially-7 AAC 18 c 4431 e](https://hackernoon.com/functional-javascript-resolving-promises-sequentially-7aac18c4431e)