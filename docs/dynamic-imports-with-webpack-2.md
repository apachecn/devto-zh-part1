# 使用 Webpack 2 进行动态导入

> 原文：<https://dev.to/kayis/dynamic-imports-with-webpack-2>

如果你正在阅读这篇文章，你可能面临许多单页应用程序都有的问题，【JavaScript 太多了！你用自己选择的工具缩小并打包了你所有的代码，但不知何故仍然以*在你的第一页加载了太多代码而告终*。

许多温泉都是通过移动连接使用的，移动连接通常没有那么快。你可能会读到，如果一个网站加载时间太长，用户就会放弃它。但是如何在不牺牲功能的情况下解决这个问题呢？

一种方法是动态或按需加载 JavaScript。

这个想法是，您只需加载用户打开的当前 URL 所需的代码。通常你的登录页面比你的其他页面要小得多。或者你有一个简单的登陆页面，显示一些漂亮的文本和一些小图片，可以很快下载和渲染。

然后，当用户知道他得到了什么时，你让他们下载剩下的，或者在他们阅读已经呈现的内容时异步下载。

总之，*性能是 UX* ，更好的 UX 意味着更快乐的用户。*最快的位是未发送的位*，所以让我们深入研究这个问题的一个解决方案: **Webpack 2**

Webpack 2 提供了一种自动将所有代码捆绑到一个文件中的方法，就像每个捆绑器一样。它还提供了一种将它打包到多个同时需要的文件中的方法，几乎是开箱即用的。

那么这是如何工作的呢？真的简单！

你只需要在代码中使用(动态)`import`函数，而不是静态求值的*常规* ES2015 `imports`。

而不是写:

```
import home from './pages/home' 
import about from './pages/about' 

const page = document.location.hash.substr(1)

if (page === 'home') home.render()
if (page === 'about ') about .render() 
```

Enter fullscreen mode Exit fullscreen mode

这将把两个页面文件放入一个包中。

你会写:

```
const page = document.location.hash.substr(1)

import('./pages/' + page)
.then(page => page.render()) 
```

Enter fullscreen mode Exit fullscreen mode

这将为所有可能的具有动态*模式*的文件生成一个包。在本例 2 中，对于`home.js`和`about.js`

那就*差不多*吧！

少了什么？

客户端上的路径需要正确。Webpack 将为每个动态导入的文件生成包，并在运行时加载*这些*包文件，而不是您的源目录中的文件。

例如，您的条目 JS 文件是`src/index.js`，但是您的输出(bundle)文件是`dist/application.js`。您可以使用以下命令导入后者:

```
<script src="dist/application.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

如果你的 HTML 文件在一个目录上面`dist/`。其他包文件将以数字为前缀。比如:`0.application.js`。这些将被动态加载到浏览器中。

Webpack 会在浏览器中抛出一个错误，说它`cannot load chunk 0`什么的。这是因为，它试图加载与您的 HTML 文件相关的其他文件，而不是与您通过 Script 标签加载的第一个 JavaScript 文件相关的文件。

要迁移它，您必须向您的 Webpack 配置添加一个`publicPath`:

```
const path = require('path')

module.exports = {

  entry:  './src/index.js',

  output: {
    filename: 'application.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: 'dist/',
  },

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您在 HTML 中加载构建的`application.js`时，所有其他生成的包文件都将在它们的路径中加载`dist/`。像`dist/0.appliaction.js`而不是`0.application.js`

我还用一个简单完整的例子创建了一个[存储库](https://github.com/kay-is/webpack-on-demand-loading)。

*编辑:System.import()已弃用。*