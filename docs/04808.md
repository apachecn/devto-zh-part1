# Vue.js 简介-第 1 章-基本设置

> 原文：<https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup>

## 系列概述

[第一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup)
[第二章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)
[第三章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)
[第四章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list)
[第五章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)

## 前言

我最近开始更深入地研究 Vue.js。我发现进入这个领域并不难，但是过一段时间后，进入这个领域会变得更难。

我决定写一系列 Vue 指南，告诉你如何在你的项目中处理某些事情。我喜欢我将向你描述的方式，但当然这不是治愈世界的整体解决方案。这只是我在和 Vue 一起工作时发现的一个最佳实践。

我会一直把代码上传到这个 github 库。

现在让我们从第一章开始。

## Node.js 模块

首先，我们需要设置一个简单的节点项目。我建议您通过命令行使用`npm init`命令来完成此操作，该命令将为您创建一个小向导来创建 package.json

```
{  "name":  "chapter1",  "version":  "1.0.0",  "description":  "Chapter 1 of the Vue Guide",  "author":  "Moritz Schramm",  "license":  "ISC"  } 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要安装一些节点模块来使用 Vue。第一个会是 Vue 本身。

```
npm install --save vue 
```

Enter fullscreen mode Exit fullscreen mode

我将使用`webpack`作为我的“编译器”,使用 babel 作为 transpiler。此外，我们将需要`raw-loader`通过 ES6 模块加载 html 文件。

```
npm install --save-dev webpack babel-core babel-loader raw-loader 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该有一个`node_modules`文件夹，并且您的 package.json 应该是这样的(版本字符串可能不同):

```
{  "name":  "chapter1",  "version":  "1.0.0",  "description":  "Chapter 1 of the Vue Guide",  "author":  "Moritz Schramm",  "license":  "ISC",  "dependencies":  {  "vue":  "2.4.4"  },  "devDependencies":  {  "babel-core":  "6.26.0",  "babel-loader":  "7.1.2",  "raw-loader":  "0.5.1",  "webpack":  "3.6.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## web pack 配置

我们将通过`webpack.config.js`配置 webpack 构建。为此，我们需要在与`package.json`相同的层次上创建该文件。

首先，我们需要定义 webpack 应该在哪里开始“编译”。根据我的设置，它应该从文件`src/main.js`开始。

```
module.exports = {
    entry: './src/main.js'
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要告诉 webpack 把捆绑的文件。我喜欢把它放在一个名为`bin`的独立文件夹中，文件名是`build.js`。

```
const path = require('path');

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname, 'bin'),
        filename: 'build.js'
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

由于 webpack 与加载器一起工作，我们需要定义我们想要使用哪个(我们安装了`raw-loader`和`babel-loader`)。我们使用的`babel-loader`。js 文件和`raw-loader`文件。html 文件。

```
const path = require('path');

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname, 'bin'),
        filename: 'build.js'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/
            },
            {
                test: /\.html$/,
                loader: 'raw-loader'
            }
        ]
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要定义一些别名来让 Vue.js 工作。第一个我们需要导入正确完整版本的 Vue.js，第二个是为了避免以后的导入路径看起来像这样:`../../../common/button/button`而不是做`app/components/common/button/button`

```
const path = require('path');

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname, 'bin'),
        filename: 'build.js'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/
            },
            {
                test: /\.html$/,
                loader: 'raw-loader'
            }
        ]
    },
    resolve: {
        alias: {
            vue: 'vue/dist/vue.js',
            app: path.resolve(__dirname, 'src')
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的 webpack 配置。

## NPM 剧本

幸运的是，npm 提供了一个脚本函数，我们可以从命令行执行命令，而不用创建一堆看起来很奇怪的 bash 脚本来执行 node_modules 文件夹深处的东西。

这些脚本可以在我们的`package.json`中定义。

```
{  "name":  "chapter1",  "version":  "1.0.0",  "description":  "Chapter 1 of the Vue Guide",  "author":  "Moritz Schramm",  "license":  "ISC",  "scripts":  {  },  "dependencies":  {  "vue":  "2.4.4"  },  "devDependencies":  {  "babel-core":  "6.26.0",  "babel-loader":  "7.1.2",  "raw-loader":  "0.5.1",  "webpack":  "3.6.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个叫做`build`的脚本和一个叫做`build:js`的脚本。

```
{  "name":  "chapter1",  "version":  "1.0.0",  "description":  "Chapter 1 of the Vue Guide",  "author":  "Moritz Schramm",  "license":  "ISC",  "scripts":  {  "build":  "npm run build:js",  "build:js":  "webpack",  },  "dependencies":  {  "vue":  "2.4.4"  },  "devDependencies":  {  "babel-core":  "6.26.0",  "babel-loader":  "7.1.2",  "npm-run-all":  "4.1.1",  "raw-loader":  "0.5.1",  "webpack":  "3.6.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`build`脚本目前只调用了`build:js`脚本，但是以后会得到更多的调用。`build:js`使用我们的`webpack.config.js`执行 webpack。

## 一些代码

在配置了一堆东西之后，我们现在创建了一些代码来测试所有的东西，并向我们的`build`脚本添加了另一个步骤。

首先，我们需要创建一个在 webpack 配置中配置的`src`文件夹。

### index.html

在这个文件夹中，我们添加了`index.html`，看起来应该是这样的:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />

    Vue Guide
</head>
<body>
    <div id="app"></div>

    <script src="build.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

`index.html`包括 webpack 生成的我们的`build.js`。我们需要将该文件复制到`bin`文件夹中，所以我添加了另一个名为`deploy:resources`的脚本，并在`build`脚本中调用它(我通过`npm install --save-dev npm-run-all`安装了`npm-run-all`，以简化调用多个 npm 脚本的方式)。

```
{  "name":  "chapter1",  "version":  "1.0.0",  "description":  "Chapter 1 of the Vue Guide",  "author":  "Moritz Schramm",  "license":  "ISC",  "scripts":  {  "build":  "npm-run-all build:js deploy:resources",  "build:js":  "webpack",  "deploy:resources":  "cp src/index.html bin/index.html"  },  "dependencies":  {  "vue":  "2.4.4"  },  "devDependencies":  {  "babel-core":  "6.26.0",  "babel-loader":  "7.1.2",  "npm-run-all":  "4.1.1",  "raw-loader":  "0.5.1",  "webpack":  "3.6.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 你好组件

这部分我暂时不解释。你可以简单地复制代码。我将在下一章更深入地探讨这个话题。

文件路径:`src/components/hello/hello.js`

```
import template from './hello.html';

export default {
    name: 'vg-hello',
    template
}; 
```

Enter fullscreen mode Exit fullscreen mode

文件路径:`src/components/hello/hello.html`

```
<h1>Hello World</h1> 
```

Enter fullscreen mode Exit fullscreen mode

### main.js

这不仅是我们 webpack 的入口点，也是我们实例化 Vue 的文件。

```
import Vue from 'vue';

new Vue({
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要告诉 Vue 最初要安装应用程序(DOM 中的渲染点，在我们的例子中是 id 为`app`的`div`)。

```
import Vue from 'vue';

new Vue({
}).$mount('#app'); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要告诉 Vue 哪个组件来呈现一个启动。

```
import hello from 'app/components/hello/hello';
import Vue from 'vue';

new Vue({
    render: (h) => h(hello)
}).$mount('#app'); 
```

Enter fullscreen mode Exit fullscreen mode

## 搞定

我们现在从命令行运行`npm run build`，你应该会找到一个包含`build.js`和`index.html`的`bin`文件夹。在你最喜欢的网络浏览器(如 Chrome 或 Firefox)中打开`index.html`，你会看到“Hello World”。

## 最后一句话

我希望你喜欢这种描述 Vue 项目设置的方式。我知道有很多不同的方法可以做到这一点(例如，不使用 webpack，使用。vue 文件，...)但是我喜欢使用 ES6 模块的方式。如果你有一些反馈，请随意添加，如果你有问题，请在 Twitter 或评论区提问。我会尽量回答。

[下一章:组件(第一部分)](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)