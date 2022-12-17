# 使用 Webpack 和 React 开发 Mendix 小部件

> 原文：<https://dev.to/ejbroeders/develop-mendix-widgets-with-webpack-and-react>

两年前的某个时候，我参与了 Javascript 和整个周边生态系统的 web 开发。这太令人兴奋了！随着 NPM 知识库 ES2015 的推出，React 感觉网络发展速度如此之快，以至于我能找到的所有博客帖子都瞬间过时了。每天都有一个新的特性或范例被引入，这是一个很酷的新东西，能够将它融入到新的项目中令人激动。

今年我进入了 Mendix，作为一名 Javascript 开发人员，我的第一个项目是小部件开发。实际上更像是一个模块。在 Finaps，我们试图做一些类似于 Plotly designer 的东西，我们可以将它包含在我们开发的一些仪表板中。这个小部件不容易在 Dojo 框架中开发；构建过程中的每一次迭代都让人感觉有点尴尬。我更希望开发一些感觉更像 Javascript 的东西。因此，经过几天的努力，我决定花更多的时间来开发一个框架，我可以利用这个框架，用我已经知道的工具来开发本地 Mendix 小部件。

这花费了一些时间和迭代，但过了一会儿，我设置的环境感觉刚刚好:使用 ES2015 甚至 ES2017 / ES7(事实上，Babel 支持的任何版本)都很容易开发，它包括 NPM 包管理器，这样我就可以包括任何新的现代库，如 React，它合并了林挺和单元测试，以便于开发。

在这篇文章中，我将解释我采取的步骤，并指导一些更重要的配置文件，并展示如何使用一个小部件的框架。如果您也想开始开发基于这个新框架的 Mendix 小部件，所有使用的代码都是免费发布的，并且可以在 Github 上找到样板文件:

[https://github.com/Finaps/boilerplate-mendix-react-widget](https://github.com/Finaps/boilerplate-mendix-react-widget)

## 核心技术

当前的 web 开发已经相当成熟，作为一名开发人员，你必须在构建过程中使用许多不同的工具。仅举几个例子:Webpack、Babel、NPM、React、ESLint、Jest 等。这一开始可能会让人望而生畏，不要担心！小部件开发所必需的基本工具将在本节中解释。

如果您正在使用样板文件，那么您可以通过首先安装 NPM，然后在样板文件所在的文件夹中发出`npm install`来安装所有这些工具。

### NPM

NPM，代表节点包管理器，是大多数 Javascript 开发的基础。它是许多项目的默认包管理器，由命令行客户端和公共包的在线数据库(称为注册表)组成。注册表包括所有流行的软件包，如 Webpack、Babel 和 React。该工具将用于下载小部件开发所需的所有其他工具。

因为它是项目的根目录，并用于下载其他软件包，所以它是唯一需要手动安装的软件包。你可以在这里找到安装文件[。](http://www.npmjs.com)

### Webpack

Webpack 是一个模块捆绑器。也就是说，Webpack 获取一个文件(称为资产)，通常是一个 Javascript 文件，并遵循该文件的所有依赖项。它查看该文件中的所有包含和导入，并构建一个依赖图。在调查之后，它将所有这些文件打包成一个文件，可以提供给客户端。

这种方法有两大优点:如果遇到小部件，客户机只需下载一个文件(这节省了大量时间！)并且使用的每个文件都可以被一个插件检查，比如 Babel，这将在下一节中描述。

在你的电脑上安装 Webpack:在你安装了 NPM 之后，从命令行发出`npm install -g webpack`。标志告诉 NPM 在你的机器上全局安装它，这样你就可以一直从命令行使用 Webpack。

### 通天塔

Babel 是一个工具，用于将 ES2015 / ES2017 / ES7 中描述的 Javascript 功能转换为任何正在使用的浏览器都可以理解的普通旧 Javascript。如果构建过程中包含了 Babel，那么您可以放心，尽管您的项目使用了很酷的新特性，如 [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) ，但它仍将在 Internet Explorer 等传统浏览器中运行。如果你想使用这些令人惊讶的新功能，并且不能强迫你的客户使用最新版本的 Chrome 或 Firefox，这一点很重要。

可以就这些新的 Javascript 特性写一篇完整的博文。而且事实上已经不少了。如果不确定 ES6/7 是什么，我建议快速谷歌一下。一种语言在如此短的时间内有如此大的发展，真是令人着迷。

关于巴别塔的另一个伟大的事情是，它允许转移 JSX 语法作出反应。在我看来，这是编写 React 应用程序的唯一优雅的方式。

Babel 不会被用作命令行工具，因此在您的开发机器上全局安装它是没有意义的。当然，它可以类似于 Webpack 的安装方式来完成，但是将它链接到一个项目来安装更有意义。要启动一个新项目:创建一个空目录，将终端中的工作目录更改为新文件夹，然后运行`npm init`。之后可以通过:`npm install -s webpack babel-core babel-loader babel-preset-env`将 Webpack 和 Babel 安装到项目中。

### 做出反应

React 是一个由脸书开发的 Javascript 库，通过使用基于组件的设计来构建用户界面。它是目前使用中最流行的 Javascript 库，它赋予了网络很多功能。当前任何包含一些设计的项目几乎都是基于组件的，尽管实际的实现并不总是 React，React 为这些接口的开发定调。如果你不知道这项技术，我劝你尽快找到一个反应教程。

要安装库，将终端移动到您的项目文件夹并运行
`npm install -s react react-dom prop-types babel-plugin-transform-react-jsx`。

## 设置好一切

将所有这些工具组合在一起的难点在于配置。互联网上有很多教程可以帮助你用 Webpack 和 Babel 建立一个简单的样板文件，但是如果你想要一些非易失性的东西，比如 Mendix 小部件，而不是简单的 Javascript 应用程序，那么这就变得非常困难了。

如果您使用的是样板文件，那么所有的配置都已经设置好了，您可以使用本节来指导您完成与默认设置不同的最重要的配置文件。

### Webpack

Webpack 是通过`webpack.config.js`配置的。本小节描述了一些典型 Webpack 配置中没有的配置标志。有关配置的完整描述，请查看[文档](https://webpack.github.io/docs/configuration.html)。在这里，示例配置文件中的每一行都比我在这篇文章中解释得更透彻。

我们需要告诉 Webpack，我们希望我们的项目被构建为一个 Mendix 小部件，它实际上是一个 AMD 模块。AMD 模块是一个 Javascript 模块，它可以很容易地集成到其他项目中。它是由 Dojo 框架开发的第一批真正模块化的 Javascript 库包之一。现在流行的标准是基于 CommonJS 的 NPM 注册表，但是对于我们的目的，我们想要一个 AMD 包。

我们可以告诉 Webpack 通过设置输出目标来构建 AMD 模块:

```
module.exports = {
    output: {
        libraryTarget: 'amd',
        ....
    }
    ....
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们希望 Webpack 使用 Babel 将我们所有新奇的 Javascript 翻译成普通的旧 Javascript。我们通过将 Babel 作为一个插件来实现:

```
module.exports = {
    ....
    module: {
        loaders: [
            {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                loader: ['babel-loader']
            }
        ]
    },
    ....
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，Mendix 小部件包含一个 Dijit 对象，它是通过一个`declare`过程定义的。很明显，这不是一个本地 Javascript 方法，因此当 Webpack 在我们的小部件中遇到一个`declare`函数调用时，它不知道如何处理它。因此，我们必须明确声明它是在外部某个地方定义的，并将由 Mendix 加载。同样适用于`WidgetBase`对象:

```
module.exports = {
    ....
    externals: {
        dojoBaseDeclare: "dojo/_base/declare",
        widgetBase: "mxui/widget/_WidgetBase"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

出于完整性考虑，最小的`webpack.config.js`应该是这样的:

```
module.exports = {
    target: 'web',
    entry: {
        app: path.join(__dirname, 'src/widget', 'HelloWorld.jsx')
    },
    output: {
        libraryTarget: 'amd',
        path: path.resolve(__dirname, 'build/widget'),
        publicPath: '',
        filename: 'HelloWorld.js'
    },
    module: {
        loaders: [
            {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                loader: ['babel-loader']
            }
        ]
    },
    externals: {
        dojoBaseDeclare: "dojo/_base/declare",
        widgetBase: "mxui/widget/_WidgetBase"
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 通天塔

巴别塔是通过`.babelrc`配置的，比较自我解释。开发 Mendix 小部件的配置要简单得多，与常规配置没有太大区别:

```
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions"]
      },
      "modules": false,
      "useBuiltIns": true
    }]
  ],
  "plugins": ["transform-react-jsx"]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 构建脚本

使用样板文件使用的默认配置，只需使用`webpack`就可以从项目的根文件夹中运行 Webpack(如果您选择不安装 Webpack global，那么命令是`./node_modules/.bin/webpack`)。接下来发生的是 Webpack 将加载配置`webpack.config.js`，它指向一个条目文件(默认为`src/widget/{WidgetName}.jsx`，其中`{WidgetName}`是在`package.json`中定义的项目名称)。它将结合 Babel 解析条目文件，并在`build/{WidgetName}/widget`目录中构建结果捆绑脚本。

要使它成为一个有效的 Mendix 小部件，需要一个`package.xml`文件，它是由样板文件中的`package.xml.js`生成的。

接下来，`build`目录中的所有内容都必须被压缩并重命名为`{widget}.mpk`，它可以直接加载到 Mendix 中。

所有这些步骤都作为 NPM 脚本包含在样板文件中。可以通过运行项目根文件夹中的`npm run build`来触发脚本。

## 你好世界

当前的设置是在`package.json`中定义 Mendix 小部件的名称，并在`src`目录中进一步开发该小部件。发布`npm install`后，此样板文件已安装并准备就绪。当`npm run build`被触发时，来自`src`目录的所有文件被获取并复制/传输到`build`目录，该目录将拥有 Mendix 小部件的源代码，从而可以在任何客户端浏览器中安全使用。`build`目录被压缩成`build/widget.mpk`，可以在 Mendix 项目中加载。出于测试目的，这个小部件文件也被 NPM 脚本复制到`mendix/widgets`，这样它就可以直接在`mendix`文件夹中的 Mendix 项目中进行测试。

例如，这个样板文件包括一个简单的`Hello World`小部件的副本，它在激活 React 库时加载，并使用 JSX 语法在屏幕上打印 Hello World。很明显，这并不令人兴奋。

为了让它更令人兴奋，让我们扩展这个小部件，以模态(弹出)方式打印`Hello World`。为此，我们将使用 [React-Bootstrap](https://react-bootstrap.github.io/) 库。

要安装引导库，请执行`npm install -s react-bootstrap`。现在编辑`src/widget/HelloWorld.jsx`来导入新的库:

```
import declare from 'dojoBaseDeclare';
import widgetBase from 'widgetBase';

import React from 'react';
import ReactDOM from 'react-dom';
import {Modal} from 'react-bootstrap';
.... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在运行`npm run build`,它会警告我们已经导入了一个项目中还没有使用的库。但是它仍然可以工作，新的小部件将包含制作弹出窗口所必需的库组件。

要让小部件显示弹出窗口，请编辑代码以呈现模式:

```
....
import {Modal} from 'react-bootstrap';

const popup = <Modal show={true}>
        <Modal.Header>
            <Modal.Title>Hello World</Modal.Title>
        </Modal.Header>
        <Modal.Body>
            <h4>I just want to say Hello</h4>
        </Modal.Body>
        <Modal.Footer></Modal.Footer>
    </Modal>;

....

    ReactDOM.render(popup, this.domNode);
.... 
```

Enter fullscreen mode Exit fullscreen mode

现在，在运行`npm run build`之后，会有一个`widget.mpk`显示何时加载`Hello World`在一个弹出窗口中。

留给读者的下一个有趣的练习是添加一个关闭按钮。在[这里](https://react-bootstrap.github.io/components.html#overlays)可以找到带有关闭按钮的模式的完整代码片段。

## 未来的可能性

这个样板文件尽可能地贴近 NPM 生态系统，这样它就可以使用其他 Javascript 开发人员正在开发的很酷的新特性。据设想，这个框架足够灵活，可以适应其他库和即将出现的开发。这在 web 开发中至关重要，因为 web 的性质变化很快。几年前的黄金标准(比如 AMD)已经过时了。作为一名开发人员，掌握所有这些变化并在开发中适应它们是非常重要的。

例如，现在 Javascript 应用程序变得相当大，开发必须包括某种单元测试。没有测试，项目将变得无法管理，这是没有人真正想要的。这一点尤其重要，因为我们看到许多`single-page`网站实际上是一个大型的 React 应用程序。单元测试有助于确保在向代码库引入新功能时不会破坏遗留代码。

这个样板文件可以扩展到通过 [Jest](https://facebook.github.io/jest/) 合并单元测试。在这个样板文件中加入 Jest 是另一个帖子的主题，这可能会在不久的将来发生。

网络上另一个让我兴奋的重大发展是[网络组装](https://en.wikipedia.org/wiki/WebAssembly)。WebAssembly 是一个可移植的堆栈机器，它承诺通过将 C 和 C++程序编译成 wasm 文件来将它们带到 web 上。wasm 文件旨在更快地解析和执行。WebAssembly 最重要的一点是它允许用许多其他语言进行 web 开发(包括 [Rust](https://blog.rust-lang.org/2016/12/22/Rust-1.14.html) ！)并将它们的生态系统包含在 web 应用程序中。

这个样板文件带来了一种面向未来的 Mendix 小部件开发的新方法。