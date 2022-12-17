# 从 Prepack 和 Webpack 开始，应用速度更快

> 原文：<https://dev.to/kayis/faster-app-starts-with-prepack--webpack>

*TL；DR 这里是一个 GitHub [库](https://github.com/kay-is/prepack-webpack-plugin-example)与示例:)*

好像我们还没有足够的工具，事情正在变得更糟...或者更好？

这次是用一个叫做[的工具预装](https://prepack.io/)！

> 一个让 JavaScript 代码运行更快的工具。

"但是，凯，JavaScript 不是已经非常快了吗？"

也许，我有什么资格告诉你呢？我几乎不能通过从 GitHub 复制和粘贴配置来设置 Webpack。

总之，**这个工具是高度实验性的**，你应该自担风险使用它，它可能会使你的 Firefox 过热。

“那到底是什么让它更快呢？”

应用程序的启动。

“这是如何实现的？”

我不知道！

但是它似乎只运行一次应用程序代码，然后在运行后保存状态。稍后，它会用产生相同状态的简化版本替换您的代码。

这应该允许你编写好的代码，可能会很慢，然后在构建时运行它，当它在浏览器中启动时，会生成不太好但很快的代码。这是通过伏都教的手段实现的。

看起来整个启动过程是 Webpack 产生的代码问题，所以值得在基于 Webpack 的项目上尝试一下。这样你就知道是否值得等待 Prepack 稳定下来，这样你就可以在将来的生产中使用它。)。

为此已经有一个 [Webpack 插件](https://www.npmjs.com/package/prepack-webpack-plugin)，所以很容易集成。

“有什么蹊跷吗？”

是的，因为您的代码是在构建时运行(并重写)的，所以您不能简单地编写需要全局浏览器 API 的代码:

```
 import { h, render } from "preact";

 const app = (
   <div id="foo">
     <span>Hello, world!</span>
     <button onClick={e => alert("hi!")}>Click Me</button>
   </div>
 );

 const domRoot = document.getElementById("app");
 render(app, domRoot); 
```

Enter fullscreen mode Exit fullscreen mode

因为 Prepack 不太了解`document`之类的东西，但幸运的是，JavaScript 并不太在乎你编写使用未定义引用的代码，直到你实际运行代码，所以我重写了我的简单示例，公开了一个在脚本加载时不运行的全局函数，如下所示:

```
 import { h, render } from "preact";

 const app = (
   <div id="foo">
     <span>Hello, world!</span>
   <button onClick={e => alert("hi!")}>Click Me</button>
 </div>
);

global.bootApp = () => {
  const domRoot = document.getElementById("app");
  render(app, domRoot);
}; 
```

Enter fullscreen mode Exit fullscreen mode

后来，在我的构建过程之外的`index.html`中，我简单地调用了`window.bootApp();`来运行我的*预打包的*代码。

在我的例子中，大小从 **24.7 KB** 膨胀到 **50.6 KB** ，但是整个事情不是关于大小，而是关于启动时的高成本计算，所以你的里程可能会有所不同。

小狗，玩玩它，告诉我它是否改善了你的生活。