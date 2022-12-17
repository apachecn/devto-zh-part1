# 用 TypeScript 和 Express 构建 API 后端——第一部分:设置

> 原文：<https://dev.to/ryanworl/building-an-api-backend-with-typescript-and-express---part-one-setup>

*这个帖子最初出现在[worl.co](https://www.worl.co)。*

TypeScript 为 JavaScript 开发人员提供了前端开发非常需要的类型安全选项，但它在节点环境中同样适用。Node 甚至包括了很多老浏览器没有的下一代 JavaScript 特性，所以你不需要任何像 Babel 或 Webpack/Browserify 这样的后期处理工具。在本教程中，我将假设您对命令行有基本的了解，并且安装了最新版本的 Node 和 NPM。我还会推荐使用 [Visual Studio 代码](https://code.visualstudio.com)，因为它对 TypeScript 有很好的现成支持。

我们将使用 Node 和 TypeScript 构建一个基本的后端 API。我们将使用流行的 [Express](https://expressjs.com) web 框架作为基本构建模块。在以后的文章中，我们将扩展到数据库持久性、代码组织、高级 TypeScript 特性等等。在这篇文章中，我们将只讨论如何设置我们的 TypeScript 环境并创建一个“Hello World！”使用 Express 路由。

如果您还没有安装 TypeScript，您可以在终端中使用`npm install -g typescript`安装它。在撰写本文时，当前的稳定版本是 2.1。

现在我们可以开始了！`cd`到你想存储这个项目的地方，然后运行`mkdir ts-express; cd ts-express`。然后运行`npm init`来创建描述应用程序的`package.json`文件。您可以保留默认值。

现在，您已经具备了开始使用 TypeScript 的所有基础。它的工作方式与任何其他节点项目非常相似，您的一般工作流将与常规 JavaScript 项目相同。我们现在可以从 NPM 安装我们的依赖项。

```
npm install --save express body-parser 
```

Enter fullscreen mode Exit fullscreen mode

因为这些是 JavaScript 库，所以我们需要 TypeScript 定义文件，以便在使用它们时获得类型检查的全部好处。这些定义文件声明了每个库的模块布局和导出类型。您可以像这样安装 Express 和主体解析器中间件的定义:

```
npm install --save @types/express @types/body-parser 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们写一些打字稿！打开您选择的编辑器进入`ts-express`目录，开始创建一个`src`目录。这个目录将保存我们的类型脚本代码。稍后我们将配置 TypeScript 编译器应该在哪里输出我们的代码。一旦创建了`src`目录，创建一个名为`app.ts`的新文件。首先，我们将把所有代码放在这个文件中，稍后我们将学习更多关于代码组织的知识。

作为基础，我们将从一个简单的 JavaScript 版本开始，然后慢慢将其转换为 TypeScript。所有有效的 JavaScript 都是有效的类型脚本。如果您有一个遗留的 JavaScript 项目要转换，您可以从将所有文件扩展名从`js`更改为`ts`开始，并逐渐增加类型，直到您满意为止。在编译这种非类型化代码时，任何关于类型的编译器错误实际上都更像是警告:TypeScript 编译器只是继续运行。

```
// src/app.ts

var express = require('express');
var app = express();

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(3000, function() {
  console.log('Example app listening on port 3000!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码启动一个 Express 服务器，添加一个根路由，然后开始监听端口 3000。这是你能得到的最简单的了。现在我们来编译一下吧！

```
$ tsc src/app.ts 
```

Enter fullscreen mode Exit fullscreen mode

显然，当我们编写更多代码时，一次编译一个文件是不愉快的，我们肯定不希望我们编译的 JavaScript 放在我们的 TypeScript 文件旁边。TypeScript 编译器有一个配置文件可以让我们解决这个问题。

这是我将在这个项目的剩余部分使用的`tsconfig.json`文件。将它放在项目的根目录中。

```
{
  "compilerOptions": {
    "module": "commonjs",
    "noImplicitAny": false,
    "removeComments": true,
    "preserveConstEnums": true,
    "outDir": "build",
    "strictNullChecks": true,
    "sourceMap": true,
    "target": "es2015"
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "**/*.spec.ts"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

`outDir`告诉编译器将我们编译的代码输出到一个名为`build`的目录中。不要担心创建它，编译器会为我们做的。`strictNullChecks`迫使我们正确地知道我们的任何变量是否可以为空，这将为您节省一些令人讨厌的调试时间，当某些东西意外为空或未定义时。`target`告诉编译器将我们的代码编译成 ES2015 JavaScript 语法。其他选项包括 ES3 和 ES5，因为它们的功能集更有限，所以您最有可能在针对浏览器时使用它们。总的来说，TypeScript 试图支持尽可能多的先前版本，因为它们具有像`async/await`这样的高级特性。

现在你可以用`tsc`编译了，希望不会收到任何错误。您的代码将输出到`tsconfig.json`中指定的`build`目录中。您现在可以运行代码:

```
$ tsc
$ node build/app.js
Example app listening on port 3000! 
```

Enter fullscreen mode Exit fullscreen mode

本节到此为止！下一节将探讨 TypeScript 的类型检查、定义不同的路径和验证。