# 承诺是如何在幕后起作用的？

> 原文：<https://dev.to/captainsafia/how-do-promises-work-under-the-hood-4edp>

所以，我知道我说过我想从这些代码阅读中休息一会儿，但是好奇心战胜了我。

我最近在做一份工作的现场面试。是的，我还没有找到工作，再过几周我就要大学毕业了。我尽量不去想(或恐慌)这件事。不管怎样，在面试的一个阶段，我的任务是实现 JavaScript `Promise`对象的内部。在我结束采访后，我决定我真的想弄清楚承诺在幕后是如何运作的。

所以我要去调查一下！

在我们开始之前，如果你对承诺有更多的了解，可能会有所帮助。如果你不熟悉，你可以查看[这个快速解释器](https://www.promisejs.org)或[MDN docs on Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

对于这种情况，我决定查看 JavaScript 实现中最流行的承诺实现。

因此，这个特定项目的代码库比 Node 的代码库小得多，这对我是个好消息！核心逻辑存储在 [`src/core.js`](https://github.com/then/promise/blob/bbf5c443af8fbf1992dc669c8f0868fba6dbd428/src/core.js) 源文件中。在这个文件中，定义了 Promise 对象。

首先，使用传递给构造函数的函数来构造一个承诺。在构造函数中，有几个内部变量被初始化，然后调用`doResolve`函数。

```
function Promise(fn) {
  if (typeof this !== 'object') {
    throw new TypeError('Promises must be constructed via new');
  }
  if (typeof fn !== 'function') {
    throw new TypeError('Promise constructor\'s argument is not a function');
  }
  this._deferredState = 0;
  this._state = 0;
  this._value = null;
  this._deferreds = null;
  if (fn === noop) return;
  doResolve(fn, this);
} 
```

`doResolve`函数接受在 Promise 的构造函数中传递的函数和对当前 Promise 的引用。所以我跳到了`doResolve`函数的定义，并试图弄清楚那里发生了什么。所以看起来这个函数会调用另一个叫做`tryCallTwo`的函数，这个函数需要两次回调。一个回调在某个值成功返回时执行，另一个回调在有错误时执行。如果回调执行成功，则使用 Promise 对象和值调用`resolve`函数，否则调用`reject`函数。

```
function doResolve(fn, promise) {
  var done = false;
  var res = tryCallTwo(fn, function (value) {
    if (done) return;
    done = true;
    resolve(promise, value);
  }, function (reason) {
    if (done) return;
    done = true;
    reject(promise, reason);
  });
  if (!done && res === IS_ERROR) {
    done = true;
    reject(promise, LAST_ERROR);
  }
} 
```

所以我想我要做的下一件事是更好地了解`tryCallTwo`正在做什么。事实证明这非常简单。基本上，它是一个轻量级的包装器函数，调用给它的第一个参数(这是一个函数)，第二个参数作为实参。

```
function tryCallTwo(fn, a, b) {
  try {
    fn(a, b);
  } catch (ex) {
    LAST_ERROR = ex;
    return IS_ERROR;
  }
} 
```

所以本质上，有了所有这些，我们传递用户在创建 Promise 对象时调用的函数。就是那个长这样的。

```
new Promise((resolve, reject) => {
  // some code goes here
}); 
```

它由上面定义的两个回调函数调用。反过来，它们继续调用这个文件中全局定义的`resolve`和`reject`函数。我决定看看`resolve`在这个特殊案例中做了什么。

该功能从快速数据检查开始。你试图解决的价值不可能是你试图解决的承诺本身。

```
function resolve(self, newValue) {
  // Promise Resolution Procedure: https://github.com/promises-aplus/promises-spec#the-promise-resolution-procedure
  if (newValue === self) {
    return reject(
      self,
      new TypeError('A promise cannot be resolved with itself.')
    );
  } 
```

然后该函数检查新值是一个对象还是一个函数。如果是，它会尝试使用`getThen`辅助函数获取在其上定义的`then`函数。

```
if (
  newValue &&
  (typeof newValue === 'object' || typeof newValue === 'function')
) {
  var then = getThen(newValue);
  if (then === IS_ERROR) {
    return reject(self, LAST_ERROR);
  } 
```

此时，该函数进行另一项检查，看`newValue`是否是一个承诺。这实质上是检查在你的`then`中返回一个承诺的情况，因为你将多个`then`链接在一起。它还做一些工作来设置先前初始化的内部变量。

```
if (
  then === self.then &&
  newValue instanceof Promise
) {
  self._state = 3;
  self._value = newValue;
  finale(self);
  return; 
```

最后，它尝试用返回的新值再次解析函数。

```
else if (typeof then === 'function') {
  doResolve(then.bind(newValue), self);
  return;
} 
```

实际上，我很高兴看到 Promise 对象的代码在很多方面与我在面试中实现的代码相似。那是一种解脱！

我发现它处理连锁的方式非常有趣。这实际上是我在面试中遇到的问题之一，看到 Promise 实现中使用的简单方法让我在智力上感到满意。

唉，我的好奇心已经满足了！我希望你喜欢这篇文章！