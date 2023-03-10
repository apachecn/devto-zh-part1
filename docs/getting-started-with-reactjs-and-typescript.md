# React.js 和 Typescript 入门

> 原文：<https://dev.to/jjude/getting-started-with-reactjs-and-typescript>

*我在我的[博客](https://jjude.com/react-with-tsc/)* 中发布了这篇文章

js 是脸书开发的一个 Javascript UI 库。在本指南中，我们将用 Typescript 将它连接起来，以在浏览器页面上显示“Hello World”。

想要学习 Typescript 的基础知识吗？阅读我的[指南](https://www.jjude.com/ts/)T3】

[![React with Typescript](img/23ff8b10144f146a820dffa69291b568.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--To-0nVTw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.jjude.com/react-tsc.svg)

我假设你已经安装了 [node.js](https://nodejs.org/en/) 。在这个帖子里，我用的是来自脸书的[纱](https://yarnpkg.com/)包经理。但是一切都将与 node.js 的默认包管理器 [npm](https://www.npmjs.com) 一起工作

将所有的 Typescript 源代码放在一个名为`src`的目录下，并在一个名为`build`的目录下编译最终输出，这是一种普遍接受的做法。因此，让我们创建这些目录。

到终端发出`mkdir src build`。

让我们启动一个新的 node.js 项目。`yarn init -y` comamnd 将用默认值创建一个新的 node.js 项目。我们需要为我们的程序增加几个模块。让我们添加它们。逐一发出以下命令。

```
yarn add react react-dom
yarn add -D typescript webpack ts-loader @types/react @types/react-dom 
```

Enter fullscreen mode Exit fullscreen mode

第二个命令中的-D 表示模块应该作为开发依赖项安装，这意味着它们不会安装在生产环境中。

让我们了解一下我们安装的模块:

*   `react`和`react-dom`是核心的 react 模块。没有他们，我们的计划就行不通；
*   `typescript`是 Javascript 的超集；
*   `webpack`是一个模块捆绑器，这意味着它可以有效地组合多个 Javascript 文件来创建单个 Javascript 文件。这提高了应用程序的性能。
*   Webpack 知道组合 Javascript 文件。但是我们用的是打字稿。这就是为什么我们需要`ts-loader`。
*   `@types/react`和`@types/react-dom`包含`react`和`react-dom`模块的类型定义。

现在，您的`package.json`应该是这样的:

```
{
  "name": "01-react-tsc",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "author": "Joseph Jude",
  "scripts": {},
  "devDependencies": {
    "@types/react": "^15.0.30",
    "@types/react-dom": "^15.5.0",
    "ts-loader": "^2.1.0",
    "typescript": "^2.3.4",
    "webpack": "^3.0.0"
  },
  "dependencies": {
    "react": "^15.6.1",
    "react-dom": "^15.6.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望调用`webpack`而不用它的路径(`./node_modules/webpack`)作为前缀。让我们全局安装它，这样我们就可以直接调用它。发布`yarn global add webpack`。

我们需要一个配置文件来使用`webpack`。让我们添加名为`webpack.config.js`的配置文件。配置文件是带有注释的自文档。

```
var path = require("path");

var config = {
  /*
   * index.tsx represents the entry point to your web application. Webpack will
   * recursively go through every "require" statement in index.tsx and
   * efficiently build out the application's dependency tree.
   */
  entry: ["./src/index.tsx"],

  /*
   * The combination of path and filename tells Webpack what name to give to
   * the final bundled JavaScript file and where to store this file.
   */
  output: {
    path: path.resolve(__dirname, "build"),
    filename: "bundle.js"
  },

  /*
   * resolve lets Webpack now in advance what file extensions you plan on
   * "require"ing into the web application, and allows you to drop them
   * in your code.
   */
  resolve: {
    extensions: [".ts", ".tsx", ".js", ".json"]
  },

  module: {
    /*
     * Each loader needs an associated Regex test that goes through each
     * of the files you've included (or in this case, all files but the
     * ones in the excluded directories) and finds all files that pass
     * the test. Then it will apply the loader to that file.
     */
    loaders: [
      {
        test: /\.tsx?$/,
        loader: "ts-loader",
        exclude: /node_modules/
      }
    ]
  },
  // When importing a module whose path matches one of the following, just
  // assume a corresponding global variable exists and use that instead.
  // This is important because it allows us to avoid bundling all of our
  // dependencies, which allows browsers to cache those libraries between builds.
  externals: {
    "react": "React",
    "react-dom": "ReactDOM"
  },
};

module.exports = config; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为 Typescript 添加`tsconfig.json`文件。复制粘贴以下代码。如果您选择使用不同的目录名，在`outDir`和`include`部分更改这些目录名。

```
{
  "compilerOptions": {
    "outDir": "./build/",
    "sourceMap": true,
    "noImplicitAny": true,
    "module": "commonjs",
    "target": "es5",
    "jsx": "react"
  },
  "include": [
    "./src/**/*"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们进入 react.js 库编程之前，只差一步了。我们需要在`package.json`的脚本部分添加一个构建步骤。

```
"scripts": {
  "build": "webpack"
}, 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行`yarn run build`了，它会调用 webpack，web pack 又会编译 Tyepscript 文件，并将它们组合成一个文件。

现在我们已经完成了所有的设置，让我们继续创建我们的应用程序文件。

在根目录下创建一个`index.html`，内容如下。这里我们使用的是 cdn 版本的`react`文件。在后续步骤中，webpack 将创建`bundle.js`。不是以`main`为`id`的空`div`标签。react 库将使用它来显示消息——现在，它将是`Hello World`。

```
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  Getting started with ReactJS and Typescript
</head>

<body>
  <div id="main"></div>

  <!-- we pick from cdn so can be cached by browsers -->
  <script src="https://unpkg.com/react@15/dist/react.min.js"></script>
  <script src="https://unpkg.com/react-dom@15/dist/react-dom.min.js"></script>

  <!-- compiled file for this project -->
  <script src="build/bundle.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建我们的逻辑文件。在`src`目录下创建`index.tsx`文件。

```
import * as React from "react";
import * as ReactDOM from "react-dom";

ReactDOM.render(
    <h1>Hello World</h1>,
    document.getElementById("main")
); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们导入的是`react`的主要模块。`ReactDom.render`有两个参数——显示什么和在哪里显示。我们将在`h1`标签中显示`Hello World`作为标题。我们将在我们创建的空`div`中显示它。

这时你的目录结构应该是这样的:

```
.
â”œâ”€â”€ build
â”œâ”€â”€ index.html
â”œâ”€â”€ package.json
â”œâ”€â”€ src
â”‚Â Â  â””â”€â”€ index.tsx
â”œâ”€â”€ tsconfig.json
â”œâ”€â”€ webpack.config.js
â””â”€â”€ yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

该执行了。在终端发布`yarn run build`。现在`webpack`将编译应用文件并创建`bundle.js`。如果你在浏览器中打开`index.html`，你会看到`Hello World`作为标题。

你可以从[库](https://gitlab.com/jjude/reactjs-exercises)下载完整的代码。然后运行`yarn install`(或者`npm install`)来安装所有的依赖项。然后用`yarn run build`(或`npm run build`)执行。