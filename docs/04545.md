# 我第一次使用 React 16.0

> 原文：<https://dev.to/letsbsocial1/my-first-time-using-react-160-ehf>

昨天我开始着手一个新的 **React** 项目，我暂时称之为 **React Universal Blog** 。我建立了基本的工作流程、目录结构和文件。我为`Jest`测试做了准备，最初模拟出样式表和图像，以确保一切正常工作。稍后我会切换到`mocking` `CSS Modules`，因为我用的是`POSTCSS`。除非你打算用`Jest Snapshot Testing`，否则没必要这么做，我就是这么做的！我很想查一下，开发中你 ***做*** 用`CSS Modules`的时候才有意义。

但这不是我写这篇文章的原因。我昨天开始了这个新项目。猜猜昨天发生了什么大事！ **React 16.0** 发布了！猜猜我的项目发生了什么。我安装了 **React 16.0** 。

在我意识到这一切之前，我决定确保我的`Jest configuration`正在工作，所以今天我在终端中键入了`npm run test`。这是输出到控制台的内容:

```
> react-universal-blog-app@1.0.0 test /Users/mariacam/Development/react-universal-blog-app
> jest

PASS  src/sum.test.js
PASS  src/App.test.js
  â— Console console.error node_modules/fbjs/lib/warning.js:33
      Warning: React depends on requestAnimationFrame. Make sure that you load a polyfill in older browsers. http://fb.me/react-polyfills Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        1.669s
Ran all test suites. 
```

注意到什么不同了吗？

```
console.error node_modules/fbjs/lib/warning.js:33
  Warning: React depends on requestAnimationFrame. Make sure that you load a polyfill in older browsers. http://fb.me/react-polyfills 
```

这让我想起了昨天发布的 **React 16.0** ，我的项目一定在使用它！我想知道我还会遇到什么新的好东西！

编码快乐！