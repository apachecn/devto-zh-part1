# 带 Laravel Mix 的简易网络包

> 原文：<https://dev.to/jonstodle/easy-webpack-with-laravel-mix-4boi>

我已经开始研究 Webpack 大约 3 次了，但是我总是在早期就不知所措，灰心丧气，就这样放弃了，认为它是一个糟糕的工作。(如果你有好的入门教程，我洗耳恭听)。

然而，有一种更简单的方法可以完全满足我的需求。如果你在过去一年左右使用 Laravel(非常流行的 PHP MVC 框架)进行开发，你可能会遇到 Laravel Mix——一个简化 Webpack 工作的工具。

虽然该工具被称为 Laravel Mix，并且是为在 Laravel 中使用而构建的，但它实际上并不是 Laravel 的一部分。Mix 可以用在其他项目中就好了；它对 Laravel 没有任何依赖性。

Laravel Mix 是 Webpack 之上的一个抽象，旨在使 Webpack 在 80%的用例中超级简单。它使用简单的语法来编译 SASS 到 CSS，minify JavaScript，从 ES2017 编译到旧的 JavaScript 语法，编译 TypeScript 和捆绑文件。

要获得 Laravel Mix，你需要通过 NPM 安装它:

```
npm install laravel-mix --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

在您的根文件夹中创建一个名为`webpack.mix.js`的文件，您就可以通过 Mix 使用 Webpack 了。

打开`webpack.mix.js`并添加以下内容:

```
let mix = require('laravel-mix'); // 1

mix
    .sass('src/app.sass', 'dist') // 2
    .js('src/app.js', 'dist'); // 3 
```

Enter fullscreen mode Exit fullscreen mode

1.  首先我们用一个`require`导入 Laravel Mix。
2.  我们调用`mix`上的`sass`将 SASS 编译成 CSS。第一个参数是`.sass`文件的路径，第二个参数是输出`.css`文件的文件夹。
3.  我们调用`mix`上的`js`来缩小和编译 JavaScript。同样，第一个参数是输入，第二个参数是输出的位置。

接下来，我们从[的文档](https://github.com/JeffreyWay/laravel-mix/blob/master/docs/installation.md#npm-scripts)中复制以下脚本模板，以便于运行混音设置。在您的`package.json`中为`scripts`属性添加以下脚本:

```
"scripts": {
    "dev": "NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "watch": "NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "hot": "NODE_ENV=development webpack-dev-server --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
    "production": "NODE_ENV=production node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
  } 
```

Enter fullscreen mode Exit fullscreen mode

`dev`将为你编译一切，而`production`也将完成所有繁重的工作，如缩小和编译 ES2017。`watch`会观察源文件的变化，并在每次保存变化时自动运行`dev`。`hot`将运行“热重装”。

我一般在开始开发的时候运行`watch`，让它运行。这样我就不用担心任何文件不是最新的了。默认情况下，如果操作系统支持，Mix 会在编译完成时发送通知。如果你觉得这很烦人，可以通过在`webconfig.mix.js`中添加`.disableNotifications()`到你的链中来关闭它。

* * *

如果你一直在努力入门 Webpack，我强烈建议你试试 Laravel Mix。它让你入门，如果你想定制它如何处理不同的文件，[文档](https://github.com/JeffreyWay/laravel-mix/tree/master/docs#readme)有一个很好的 Mix 所有特性的列表。

如果你想更深入，你可以在`webpack.mix.js`中添加`.webpackConfig(...)`到你的链中，并传递任何定制的配置。Mix 将对原始配置和自定义配置进行深度合并。当然，您也可以直接编辑 webpack.config.js 文件。

编码快乐！