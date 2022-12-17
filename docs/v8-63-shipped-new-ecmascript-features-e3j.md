# V8 6.3 提供了新的 ECMAScript 特性

> 原文：<https://dev.to/shisama/v8-63-shipped-new-ecmascript-features-e3j>

V8 于 2017 年 10 月 25 日发布了 6.3 版本(参见: [V8 JavaScript 引擎](https://v8project.blogspot.jp/2017/10/v8-release-63.html) )
它包括了来自 [ECMAScript 提案阶段-3](https://github.com/tc39/proposals#stage-3)T5 的新特性，看起来很可能它们也将很快在 Node.js 中实现

现在，你可以用`--hamony`选项或第三方库来尝试它们。
我在 [GitHub](https://github.com/shisama/v8-6.3-es-features) 上开发了演示

# 动态导入

[规格](https://tc39.github.io/proposal-dynamic-import/)

使用这个特性可以在运行时动态加载 JavaScript 应用程序的一部分。如果你想使用 moduleA 或 moduleB 中的函数，你可以编写如下代码。

```
if (member.isLoggedIn()) {
  moduleA.func();
} else {
  moduleB.func();
} 
```

Enter fullscreen mode Exit fullscreen mode

使用静态导入，您必须导入两个模块。

```
import moduleA from './moduleA';
import moduleB from './moduleB';

if (member.isLoggedIn()) {
  moduleA.func();
} else {
  moduleB.func();
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，现在您可以只动态导入您希望与动态导入功能一起使用的模块。

```
if (member.isLoggedIn()) {
  import('./moduleA.js') // load moduleA here
    .then(module => {
      module.func();
    });
} else {
  import('./moduleB.js') // load moduleB here
    .then(module => {
      module.func();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，您可以将动态导入与 loop 或 promise 一起使用。

```
for (let i = 0; i < MAX; i++) {
  import(`module${i}.js`)
    .then(module => {
      module.func();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

```
promiseFunc()
  .then(res => {
    return import('./module.js');
  })
  .then(module => {
    module.func(data);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

函数 import()返回 Promise 对象，您可以使用 async/await。

```
(async() => {
  if (member.isLoggedIn()) {
    const module = await import('./moduleA.js') // load moduleA
    module.func();
  } else {
    const module = await import('./moduleB.js') // load moduleB
    module.func();
  }
})() 
```

Enter fullscreen mode Exit fullscreen mode

[演示](https://shisama.github.io/v8-6.3-es-features/demo/dynamic_import.html)
使用 Chrome 63 或更高版本，检查控制台或网络面板，可以看到运行时动态加载的模块。
代码如下。

```
(async() => {
    let count = 0;
    // load hello1、hello2、hello3 in a random order at runtime
    let id = setInterval(async() => {
      const i = Math.floor(Math.random() * 3) + 1;
      const module = await import(`./import_modules/hello${i}.js`);
      module.hello();
      count++;
      if (count === 10) {
          clearInterval(id);
      }
     }, 1000);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

使用 [node-es-module-loader](https://www.npmjs.com/package/node-es-module-loader) ，可以在 Node.js 中运行。

# 异步迭代

[规格](https://tc39.github.io/proposal-async-iteration/)

又名异步迭代器/生成器
它支持异步函数的异步迭代数据。
如果你不知道迭代器和生成器，读读[这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

到目前为止，已经可以用 Promise.all 并行运行异步函数或 API

```
(async() => {
  const dataList = await Promise.all([
    fetch('https://qiita.com/api/v2/tags/Node.js'),
    fetch('https://qiita.com/api/v2/tags/JavaScript'),
    fetch('https://qiita.com/api/v2/tags/npm'),
  ]);
  for (const data of dataList) {
    console.log(data);
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

然而，调用许多函数或 API 可能会使代码过于笨拙。异步迭代器使其易于阅读。

```
(async() => {
  const promises = [];
  for (const tag of ["Node.js", "JavaScript", "npm"]) {
    promises.push(fetch(`https://qiita.com/api/v2/tags/${tag}`));
  }

  for await (const data of promises) {
    console.log(data)
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

异步生成器使得定制迭代成为可能。
[演示](https://shisama.github.io/v8-6.3-es-features/demo/async_iteration.html)
用 Chrome 63 或更高版本，检查控制台或网络面板可以看到 api 被调用了几次。
代码如下。使用[轴](https://github.com/axios/axios)。

```
/*
 * get random number
 */
async function* gen() {
  while (true) {
    const res = await axios.get('https://www.random.org/decimal-fractions/?num=1&dec=10&col=1&format=plain&rnd=new');
    const num = res.data;
    yield Number(num);
  }
}

(async() => {
  const BREAK = 0.8;
  for await (const num of gen()) {
    console.log(num);
    if (num > BREAK) {
      console.log("over", BREAK);
      break;
    }
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

在 Node.js 中，需要使用`--harmony_async_iteration`选项。

# 无极

[规格](https://tc39.github.io/proposal-promise-finally/)

如你所知，这就像是最后的尝试。到目前为止，如果你想执行一些代码，而不管是否会发生错误，你必须在承诺的 then()和 catch()块中编写这些代码。

```
promiseFunc()
  .then(() => {
    someFunction();
    closeFunction(); // for successful
  })
  .catch(err => {
    console.log(err);
    closeFunction(); // for error
  }); 
```

Enter fullscreen mode Exit fullscreen mode

然而，现在，Promise.prototype.finally 允许您编写类似于 try-catch-finally 模式的代码。

```
promiseFunc()
  .then(() => {
    someFunction();
  })
  .catch(err => {
    console.log(err);
  })
  .finally(() => {
    closeFunction();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以将 try-catch-finally 与 async/await 模式一起使用。

```
(async() => {
  try {
    await promiseFunc();
    await someFunction();
  } catch (err) {
    console.log(err);
  } finally {
    closeFunction();
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

[演示](https://shisama.github.io/v8-6.3-es-features/demo/promise_finally.html)
代码如下。

```
Promise.resolve("resolve")
  .then(val => {console.log(val);return "then"})
  .then(val => {console.log(val);throw new Error("catch")})
  .catch(err => {console.log(err.message)})
  .finally(() => {console.log("finally")}); 
```

Enter fullscreen mode Exit fullscreen mode

使用 Chrome 63 或更高版本，检查控制台面板，可以看到 resolve()，then()，catch()和 finally()被调用。

在 Node.js 中，需要使用`--harmony_promise_finally`选项。

# 进一步阅读

*   [V8 JavaScript 引擎:V8 版本 6.3](https://v8project.blogspot.jp/2017/10/v8-release-63.html)
*   [tc39/提案-动态-导入](https://tc39.github.io/proposal-dynamic-import/)
*   [tc39/提议-异步-迭代](https://tc39.github.io/proposal-async-iteration/)
*   [tc39/求婚-承诺-最终](https://tc39.github.io/proposal-promise-finally/)
*   [异步迭代器和生成器-JakeArchibald.com](https://jakearchibald.com/2017/async-iterators-and-generators/)
*   [promise . prototype . finally | blog . jxck . io](https://blog.jxck.io/entries/2017-08-10/promise-finally-proposal.html)

# 感谢

[@shimataro999](https://twitter.com/shimataro999) 和 [@mackles93](https://twitter.com/mackles93) 审核了这篇帖子。

谢了。