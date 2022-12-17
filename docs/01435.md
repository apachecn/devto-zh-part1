# 等待 JavaScript 中的异步承诺

> 原文：<https://dev.to/rhymes/awaiting-for-async-promises-in-javascript-3gbd>

*这篇文章起源于对 [@warrend](https://dev.to/warrend) 的文章[澄清 Async 和 Promises](https://dev.to/warrend/clarifying-async-and-promises-4h38) 的评论，我决定把它作为一篇单独的文章重新发布，因为它可能对其他人有用。*

前几天，我在探索承诺的“then/catch”模式之外的可能性，因为对我来说，它看起来仍然像回调，更整洁和干净，但为了可读性，必须有更好的方法。

在 Python 领域(参见影响 Node.js 的 [Twisted framework)这个问题已经遇到了。在 Twisted 中，承诺被称为“推迟”，但问题是一样的:级联回调、错误处理程序、对错误处理程序的回调、兄弟回调和错误处理程序仍然会变得一塌糊涂，难以阅读和理解:](https://nodejs.org/en/about/) 

```
.then(() => {}).then(() => {}).then(() => {}).catch((err) => {}) 
```

Enter fullscreen mode Exit fullscreen mode

或者在扭曲

```
.addCallback(function).addCallback(function).addCallback(function).addErrback(errHandler) 
```

Enter fullscreen mode Exit fullscreen mode

他们想出的是:

> 一个名为 inlineCallbacks 的装饰器，它允许您在不编写回调函数的情况下处理延迟。

所以在 Twisted 中你可以这样做:

```
@inlineCallbacks
def getUsers(self):
    try:
        responseBody = yield makeRequest("GET", "/users")
    except ConnectionError:
       log.failure("makeRequest failed due to connection error")
       return []

   return json.loads(responseBody) 
```

Enter fullscreen mode Exit fullscreen mode

`makeRequest`返回一个 deferred(一个承诺),这样你就可以等待响应返回，如果出现错误，你可以在那里处理，然后用`try...except`( JS 中的 try/catch)处理。在最新的 Python 版本中，你甚至可以这样做:

```
async def bar():
    baz = await someOtherDeferredFunction()
    fooResult = await foo()
    return baz + fooResult 
```

Enter fullscreen mode Exit fullscreen mode

所以你基本上可以使用`await`for deferred/promises 来解析和编写看起来同步的代码，而不是附加回调，这就把我带回到了 JavaScript 和 async/await(Python 的相同关键字，不知道哪个先出现:D)。

您可以使用 async/await 来编写可读性更好的代码，而不是将回调和错误处理程序附加到您的承诺上:

```
async function bar() {
  const a = await someFunction();
  const b = await someOtherFunction();
  return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得韦斯·博斯的这段视频很有启发性:

[https://www.youtube.com/embed/9YkUCxvaLEk](https://www.youtube.com/embed/9YkUCxvaLEk)

*   [扭曲延期](https://twistedmatrix.com/documents/current/core/howto/defer-intro.html)