# 在包含 Visual Studio 代码的 JavaScript 项目中保持 TypeScript 的优势

> 原文：<https://dev.to/kahlil/keeping-typescript-benefits-in-a-javascript-project-with-visual-studio-code-1235>

当我开始 [Grit](https://www.producthunt.com/upcoming/grit) 的时候，我很兴奋地发现，电子团队正在用电子发布类型，这些类型在你的工具中公开了电子 API。因为我是 TypeScript 的爱好者，所以我设置了我的开发环境来用 TypeScript 编写我的代码。

在为我的应用程序写了几天 TS 代码来转换它之后，我开始对我正在转换代码的事实感到恼火。原因如下:

在 Electron 中，你为一个非常强大的浏览器编写代码。ES2015 已完全实施，但不包括 ES 模块。这意味着我可以直接为浏览器编写的代码对我来说已经非常接近理想了，以至于从其他地方移植代码是错误的。所以我把我的项目转换回 JavaScript。

在与 Babel 和 TypeScript 打交道多年后，直接为浏览器编写 ES2015+代码感觉非常自由。

没有要解码的源映射，没有要管理的类型。

不过，丢失类型会有点烦人。对我个人来说，在这个单人项目中，我喜欢类型，特别是因为它们增强了工具性。

谢天谢地，有 VSCode！

VSCode 引入了一个叫做 [`jsconfig.json`](https://code.visualstudio.com/docs/languages/jsconfig) 的东西。它是一个配置文件，告诉 VSCode 包含它的文件夹是一个 JavaScript 项目。

从[code.visualstudio.com](https://code.visualstudio.com/docs/languages/jsconfig):

> `jsconfig.json`是`tsconfig.json`的派生，是 TypeScript 的配置文件。`jsconfig.json`是将`"allowJs"`属性设置为 true 的`tsconfig.json`。

添加该文件告诉 VSCode 打开基于 TypeScript 语言服务的 [JavaScript 语言服务](https://github.com/Microsoft/TypeScript/wiki/JavaScript-Language-Service-in-Visual-Studio)。这在整个项目中为您提供了强大的智能感知功能。

这意味着普通 JavaScript 项目会出现自动完成和键入错误。类型是由 TypeScript 类型定义文件以及 JSDoc 注释推断出来的。

## 为电子设置 jsconfig.json

为了让它为我的电子设置满意地工作，我必须配置一些东西。jsconfig.json 只是一个 tsconfig.json，所以选项是相同的。

首先，我排除了`node_modules`，因为我不想让 VSCode 对我所有的依赖项进行类型检查。

```
"exclude": ["node_modules"] 
```

Enter fullscreen mode Exit fullscreen mode

在`compileroptions`属性中，我将`checkJs`属性设置为`true`，以便尽可能对 JavaScript 代码进行类型检查。

因为我(不得不)在 electronic 中使用 CommonJS 模块，所以我不得不将`module`属性设置为`commonjs`，这样`index.js`文件就被解析为 CommonJS 风格。

最后但同样重要的是，为了能够编写 ES2015+代码而不出现警告，我将`target`属性设置为`es2017`。

你可以在这里看看我的配置文件[。](https://github.com/kahlil/grit/blob/master/jsconfig.json)

## 为 HyperHTMLElement 添加类型定义

我必须为 HyperHTMLElement 创建一个类型定义文件，因为 JavaScript 语言服务不喜欢我在所需的模块上使用`default`属性。这是问题中的代码:

```
const HyperHTMLElement = require('hyperhtml-element').default; 
```

Enter fullscreen mode Exit fullscreen mode

我不厌其烦地在定义中添加了所有的类属性，这给了我在 VSCode 中非常棒的代码完成特性。如果你需要，你可以从[这里](https://github.com/kahlil/grit/blob/master/types/hyperhtml-element.d.ts)取。

这种设置允许我直接为浏览器编写现代 JavaScript 代码，并受益于许多 TypeScript 特性。爱死了。