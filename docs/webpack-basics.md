# Webpack 基础

> 原文：<https://dev.to/kayis/webpack-basics>

*TL；我用示例[创建了一个存储库](https://github.com/kay-is/webpack-example/tree/master)这里*:)

许多人似乎喜欢 [Webpack](https://webpack.js.org/) 并将其用于他们的日常网络捆绑过程，但我从许多其他人那里听说，他们根本无法理解如何在第一时间使用它。所以我有了创建一个(非常)基本的项目的想法，并写下配置的部分，只是 HTML，CSS 和 JS，没有什么花哨的。

# 安装

对于这个示例项目，我使用了以下依赖关系:

```
"devDependencies": {
  "css-loader": "^0.28.4",
  "style-loader": "^0.18.2",
  "html-webpack-plugin": "^2.28.0",
  "http-server": "^0.10.0",
  "webpack": "^3.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

两个加载器模块能够通过 JS 加载 CSS。

一个 Webpack 插件，让我创建 HTML 文件，该文件将自动为创建的包添加脚本标签。

一个 HTTP 服务器，它只提供静态文件。

最后，Webpack 本身。

虽然 Webpack 和 http-server 是*全局*节点模块——它们通过命令行运行——但您可以在本地将它们安装在`devDependencies`中，npm 脚本可以伪全局访问它们。

```
"scripts": {
  "setup": "npm i && npm run build && npm start",
  "build": "webpack",
  "start": "http-server -c-1 ./build"
}, 
```

Enter fullscreen mode Exit fullscreen mode

`npm run build`只是`webpack`的别名，但是不需要全局安装 Webpack 也能工作。对于`npm start`也是一样，它只是 http-server 调用的别名。

# 基本配置结构

Webpack 配置文件，通常命名为`webpack.config.js`，只是一个 JS 文件，在 [Node.js](https://nodejs.org/en/) 中执行。它必须导出一个配置对象。

这意味着首先也是最重要的是，你基本上可以使用其中的所有节点模块，并像你习惯的那样编写 JavaScript。这为您创建配置对象提供了很大的灵活性。

基本配置文件可能如下所示:

```
 const path = require("path");
const HtmlPlugin = require("html-webpack-plugin");

const html = new HtmlPlugin({ template: "./modules/index.html" });
const outputPath = path.resolve(__dirname, "build");

module.exports = {
  entry: {
    application: "./modules/index.js"
  },

  output: {
    filename: "[name].[chunkhash].js",
    path: outputPath
  },

  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"]
      }
    ]
  },

  plugins: [html]
}; 
```

## 导入和预配置

```
const path = require("path");
const HtmlPlugin = require("html-webpack-plugin");

const html = new HtmlPlugin({ template: "./modules/index.html" });
const outputPath = path.resolve(__dirname, "build"); 
```

Enter fullscreen mode Exit fullscreen mode

首先，导入创建配置对象所需的模块。

在这里，我导入了节点路径模块和 [Webpack HTML 插件](https://github.com/jantimon/html-webpack-plugin)。

接下来，编写在创建配置对象之前需要做的事情。如果你喜欢，你可以这样做，我只是喜欢这样。通常你会有很多插件，如果它们都是内嵌创建的，这会变得很笨拙。

在这个例子中，我创建了一个 HTML 插件的实例，并获得了输出目录的绝对路径。

## 创建配置对象

下一部分是 config 对象的创建。它有四个重要部分:`entry`、`output`、`module`和`plugins`。

### 条目

```
entry: {
  application: "./modules/index.js"
}, 
```

Enter fullscreen mode Exit fullscreen mode

`entry`告诉 Webpack 从哪里开始。为此，你必须理解 Webpack 是以树形结构工作的。它获取一个或多个条目，并在这些条目文件中查找，如果导入了其他文件，这将一直持续下去，直到没有文件再导入其他文件。

如果在别处没有配置任何不同，Webpack 将为每个条目创建一个包文件，在本例中只有一个。

使用多个包文件的另一个原因是动态导入。如果你在某处使用了`import("my-module").then(myModule => ...)`而不是`import myModule from "my-module"`，Webpack 会自动创建额外的文件，这些文件会在调用`import`时导入。

### 输出

```
output: {
  filename: "[name].[chunkhash].js",
  path: outputPath
}, 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们配置包文件的名称。你可以使用一个*固定的*名称，或者一些占位符。我用了`[name]`和`[chunkhash]`。

`[name]`将被替换为来自`entry`对象的一个键，或者一个动态生成的 ID。例如，如果您使用动态导入，它们将由 Webpack 自动命名。

`[chunkhash]`将被替换为一个散列，它反映了这个包的内容。也就是说每次你修改了一个文件它就会改变。这会将每个新版本强制放入一个新的包文件中，这在您希望您的用户只下载最新版本时很有帮助。在开发模式下，我通常只使用`[name]`,这样我就不会有上百个包。

`path`必须是输出目录的绝对路径，所以我在运行时借助 Nodes `path`模块生成了它。

### 模块

```
module: {
  rules: [
    {
      test: /\.css$/,
      use: ["style-loader", "css-loader"]
    }
  ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

这里定义了模块。通常它们只是由一堆与加载器相关联的规则组成。

当我导入一个 CSS 文件时，请通过`style-loader`和`css-loader`运行它。

*Webpack 只知道 JavaScript* ，所以你得告诉它你还需要什么。加载器通常会实现一些机制来将非 JavaScript 资产嵌入到 JavaScript 中，或者使它们可以从 JS 中访问。

这也可以与 Babel 或 TypeScript 一起使用，因此您的 ES2015 或`.ts`文件被编译成 ES5 或其他文件。它甚至可以处理 LESS、SASS、图像、字体等。过去分词（past participle 的缩写）

这一切都是在每个文件的基础上*工作的，这意味着，加载程序一次只查看*一个文件*，并试图以某种方式转换它。*

### 插件

```
plugins: [html] 
```

Enter fullscreen mode Exit fullscreen mode

插件可以处理多个文件。这允许从包中提取所有 CSS 文本，并将它们放入一个单独的 CSS 文件中，或者创建一个 HTML 文件，该文件将包含所有创建的 JavaScript 包。

在这个例子中，我使用了`[chunkhash]`作为包文件名的一部分，这导致每次我用修改过的代码构建时都有不同的文件名。

HTML 插件允许我从我喜欢的模板创建一个 HTML 文件，并用包文件的脚本标签填充它。所以每次我运行 Webpack，我都会得到一个新的已经包含正确文件名的`index.html`。

# 非配置因素

就像我上面提到的，配置不是影响输出的唯一因素。

如果您使用动态导入，这会导致隐式代码分割。

在我的例子中，我用了 4 页纸。首页，关于，联系和加载。Home 和 loading 是静态导入*，about 和 contact 是动态*。**

 **Webpack 可以从`import("./pages/" + variable)`中推断出动态页面都在`./pages`中，因此它为该目录中的所有文件创建包，但不包括那些已经静态导入*的文件*。

当您访问 web 服务器上的示例时，您会看到这两个动态包仅在单击链接后才被加载。作为构建过程的一部分创建的其他中间包 Webpack 不被浏览器加载。

# 备注

你可以用`webpack --watch`运行 Webpack，它会在你每次修改文件的时候触发一次编译。Webpack 甚至有自己的开发服务器，可以用来进一步改进开发过程，动态替换热模块等。

您可以有多个配置文件，并通过`webpack --config <name>`运行它们，或者基于某个环境变量创建它们。一些库默认运行在开发模式下，并且在为生产而编译时要求你做不同的事情，所以通常你不会轻易得到一个简单的配置文件。

# 结论

Webpack 是一个强大的工具，通常很难掌握，特别是如果您刚刚学习 JavaScript，突然被迫记住 Webpack 的概念，但仔细观察，它们并不多，甚至动态代码分割和加载也经常在没有显式配置的情况下很好地处理。**