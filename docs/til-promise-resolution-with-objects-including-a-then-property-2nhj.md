# TIL:包含 then 属性的对象的承诺解析

> 原文：<https://dev.to/stefanjudis/til-promise-resolution-with-objects-including-a-then-property-2nhj>

**TL；博士**

*当你用一个定义了`then`方法的对象解析一个承诺时，就会发生“标准承诺行为”。将立即使用`resolve`和`reject`参数执行`then`方法。用其他值调用`then`会覆盖初始承诺解析值。这种行为支持递归承诺链。*

*加载 JavaScript 模块的相当新的`import`方法也不例外。*

* * *

最近，两条涉及承诺和动态进口的推文引起了我的注意。我花了两个小时阅读规范，这篇文章分享了我的思考过程和我对承诺和承诺链的了解。

### 推文 1:一种“有点”黑掉顶级 await 的方法

苏尔马分享了[“一个让顶层等待工作的黑客”](https://twitter.com/DasSurma/status/1101461224317956101)。

您可以在 HTML 中包含一个内联脚本`type="module"`，它可以动态导入另一个模块。

```
<script type="module">
  import('./file.mjs');
</script> 
```

Enter fullscreen mode Exit fullscreen mode

该模块本身导出一个`then`函数，该函数将被立即执行，无需任何调用。

```
// file.mjs
export async function then() {
  // yay!!!      I can use async/await here
  // also yay!!! this function will be executed automatically
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用该行为将`file.mjs`定义为应用程序的入口点，并在`then`函数中使用 async/await right await。

**重要细节:`then`功能是自动执行的。**

### 推文 2:动态导入的阻止行为

[Johannes Ewald 分享了](https://twitter.com/Jhnnns/status/1100074123118735360)当导入的返回值包含一个`then`函数时，动态导入可以“阻塞”代码执行。

```
// file.mjs
export function then() {}

// index.mjs
async function start() {
  const a = await import('./file.mjs');
  // the following lines will never be executed
  console.log(a);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的片段不会记录任何东西。

**重要细节:`import('./file.mjs')`永不解决。**

## 承诺解决流程

你在上面的例子中看到的行为与`import`规范无关([一个 GitHub 问题](https://github.com/tc39/proposal-dynamic-import/issues/47)非常详细地描述了这种行为)。描述承诺解析过程的 ECMAscript 规范反而是基础。

```
8\.  If Type(resolution) is not Object, then
      a. Return FulfillPromise(promise, resolution).

9\.  Let then be Get(resolution, "then").

10\. If then is an abrupt completion, then
      a. Return RejectPromise(promise, then.[[Value]]).

11\. Let thenAction be then.[[Value]].

12\. If IsCallable(thenAction) is false, then
      a. Return FulfillPromise(promise, resolution).

13\. Perform EnqueueJob(
      "PromiseJobs", PromiseResolveThenableJob, « promise, resolution, thenAction »
    ). 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步一步地检查实现承诺的可能性。

> 如果 Type(resolution)不是 Object，则返回 FulfillPromise(promise，resolution)

如果您用一个字符串值(或任何不是对象的值)解析一个承诺，这个值将是承诺解析。

```
Promise.resolve('Hello').then(
  value => console.log(`Resolution with: ${value}`)
);

// log: Resolution with: Hello 
```

Enter fullscreen mode Exit fullscreen mode

### Promise 解析一个包含`then`的对象，该对象是一个`abruptCompletion`

> 让然后被得到(决议，“然后”)。如果 then 是一个突然完成，那么返回 RejectPromise(promise，then。[[值]])。

如果您用包含一个`then`属性的对象来解析一个承诺，而该属性的访问会导致一个异常，这将导致一个被拒绝的承诺。

```
const value = {};
Object.defineProperty(
  value,
  'then',
  { get() { throw new Error('no then!'); } }
);

Promise.resolve(value).catch(
  e => console.log(`Error: ${e}`)
);

// log: Error: no then! 
```

Enter fullscreen mode Exit fullscreen mode

### Promise 解析一个包含`then`的对象，该对象不是函数

> 那就这样吧。[[值]]。如果 is callable(the action)为 false，则返回 FulfillPromise(promise，resolution)。

如果您用一个包含非函数的`then`属性的对象来解析一个承诺，则该承诺会用该对象本身来解析。

```
Promise.resolve(
  { then: 42 }
).then(
  value => console.log(`Resolution with: ${JSON.stringify(value)}`)
);

// log: Resolution with: {"then":42} 
```

Enter fullscreen mode Exit fullscreen mode

### Promise 解析一个包含`then`的对象，它是一个函数

现在，我们来到激动人心的部分，这是递归承诺链的基础。我开始深入兔子洞描述完整的功能，但是它将包括对 ECMAScript 规范其他几个部分的引用。详细的讨论超出了这篇文章的范围。

这最后一步的关键部分是，当一个 promise 用一个包含`then`方法的对象解析时，解析过程将调用`then`和通常的 promise 参数`resolve`和`reject`来评估最终的解析值。如果`resolve`没有被召唤，承诺将不会被解决。

```
Promise.resolve(
  { then: (...args) => console.log(args) }
).then(value => console.log(`Resolution with: ${value}`));

// log: [fn, fn]
//        |   \--- reject
//     resolve

// !!! No log of a resolution value 
```

Enter fullscreen mode Exit fullscreen mode

这种定义的行为导致了第二个 Tweet 示例的永远待定的承诺。没有被召唤，因此承诺永远不会实现。

```
Promise.resolve(
  { 
    then: (resolve) => { 
      console.log('Hello from then');
      resolve(42);
    }
  }
).then(value => console.log(`Resolution with: ${value}`));

// log: Hello from then
// log: Resolution with: 42 
```

Enter fullscreen mode Exit fullscreen mode

## 这一切都联系在一起

幸运的是，推特上分享的行为现在对我来说有意义了。此外，这是您每天用来递归链接 promise 的描述行为。

```
(async () => {
  const value = await new Promise((resolve, reject) => {
    // the outer promise will be resolved with 
    // an object including a `then` method
    // (another promise)
    // and the resolution of the inner promise
    // becomes the resolution of the outer promise
    return resolve(Promise.resolve(42));
  });

  console.log(`Resolution with: ${value}`);
})();

// log: Resolution with: 42 
```

Enter fullscreen mode Exit fullscreen mode

### 令人惊讶的边缘案例

当使用`then` -hack 时，你必须非常小心，可能会有解析过程导致意外行为的情况。

```
Promise.resolve({
  then: resolve => resolve(42),
  foo: 'bar'
}).then(value => console.log(`Resolution with: ${value}`));

// log: Resolution with: 42 
```

Enter fullscreen mode Exit fullscreen mode

即使上面的承诺解决了一个包含几个属性的对象，你得到的也只是`42`。

## 动态导入也不例外，遵循标准的承诺解析流程

当您使用动态`import`函数加载 JavaScript 模块时，`import`遵循相同的过程，因为它返回一个承诺。导入模块的解析值将是一个包含所有导出值和方法的对象。

对于导出一个`then`函数的情况，指定的承诺处理开始评估整体解决方案应该是什么。`then`函数可以覆盖这个模块中可能包含的所有内容。

```
// file.mjs
export function then (resolve) {
  resolve('Not what you expect!');
}

export function getValue () {
  return 42;
}

// index.mjs
import('./file.mjs').then(
  resolvedModule => console.log(resolvedModule)
);

// log: Not what you expect 
```

Enter fullscreen mode Exit fullscreen mode

我肯定会避免命名我的函数`then`。找到这样的 bug 可能需要几分钟。🙈

今天就到这里吧！我希望这是有用的，并尽快交谈。👋