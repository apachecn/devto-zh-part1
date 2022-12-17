# 将 JavaScript 项目转换为 Typescript，一次一个文件

> 原文：<https://dev.to/suhas_chatekar/converting-a-javascript-project-to-typescript-one-file-at-a-time>

# 将 JavaScript 应用程序转换为 Typescript

JavaScript 很好，但是像我一样，如果你来自静态类型的语言，那么当项目变大时，处理类型的缺乏就变得有点烦人了。幸运的是有 Typescript，但是回溯性地添加 Typescript 可能不是一件非常简单的工作。最近，我成功地将一个相当大的 JavaScript 项目转换成了 Typescript，一次转换一个文件。一次将一个文件转换成 Typescript 是非常强大的，因为这样您就可以增量地进行更改，而不必停止交付您的产品所有者想要的特性。我试着用不同的方式做这件事。在这里，我会告诉你一个有效的方法。

这是一本冗长的读物。我将内容分为三个主要部分

1.  添加 typescript 配置和 webpack
2.  添加类型声明文件
3.  将代码转换成类型脚本

本文的源代码在 [GitHub](https://github.com/schatekar/js-to-ts) 上。这是一个非常简单的计算器应用程序，只有两个文件。我认为这些文件足以演示这种方法。最初的 javascript 源代码在[主分支](https://github.com/schatekar/js-to-ts)中。每一节末尾更改的源代码都在同一个存储库中的适当分支中。

那我们直接开进去吧。

# 1。添加 typescript 配置和 webpack

任何类型脚本代码在运行之前都必须被编译成 JavaScript。这就是 webpack 派上用场的地方。如果您以前从未使用过 Webpack，那么我建议您在继续之前阅读 webpack 的详细介绍。

我们使用下面的命令
开始安装 webpack

```
npm install --save-dev webpack 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将 webpack 添加为开发依赖项。这是因为它只用于将 typescript 代码转换成 javascript。接下来，我们需要一个 webpack 配置。在项目的根目录下添加一个名为`webpack.config.js`的新文件，并在其中添加以下内容

```
var path = require('path');
var webpack = require('webpack');

module.exports = {  
  entry: { 
    index: "./index.ts"
  },
  target: 'node',
  module: {
    loaders: [
      { test: /\.ts(x?)$/, loader: 'ts-loader' },      
      { test: /\.json$/, loader: 'json-loader' }
    ]
  },
  plugins: [
    new webpack.DefinePlugin({'process.env.NODE_ENV': '"production"'})
    ],
  resolve: {
    extensions: ['.ts', '.js', '.json']
  },
  output: {
    libraryTarget: 'commonjs',
    path: path.join(__dirname, 'lib'),
    filename: '[name].js'
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们快速浏览一下这个文件的内容。我们已经导入了`path`模块，使得在配置的输出部分进行一些路径操作变得容易。我们还导入了 webpack，在`plugins`部分定义了一个插件。我们先不要担心这个。

该文件基本上只是导出一个 JSON 对象，webpack 将其用作配置。Webpack 有大量的配置选项，但我们在这里配置的是 typescript 项目所需的最少选项。让我们来看看我们在这个对象中定义的每个配置

## 条目

这告诉 webpack 从哪里开始传输文件。Webpack 将从`entry`中指定的文件开始，将它们转换成 JS(见下一节`module`),然后遍历这些模块导入的每个模块，直到到达树的末尾。我们不必只有一个入口点。我们可以在这里提供任意数量的入口点。我们已经指定`index.ts`作为我们的切入点。该文件尚不存在。我们最终将把我们的进入舱`index.js`转换成`index.ts`。

## 目标

Target 告诉 webpack 您想在哪里运行最终的 Javascript 代码。这很重要，因为生成的在服务器端运行的代码不同于生成的在浏览器中运行的代码。对于这个例子，我们指定了`node`,这是在服务器端运行的代码

## 模块

这是最神奇的地方。我们已经在这个对象的`loaders`部分指定了。Webpack 使用不同的加载器来传输文件。在我们的例子中，我们有一个`ts-loader`来传输任何类型脚本文件，还有一个`json-loader`，我把它留在那里，以防我们以后添加 json 文件。加载程序需要单独安装，它们作为自己的 NPM 软件包提供。对于我们的配置，我们需要使用下面的命令安装`ts-loader`和`json-loader`。

```
npm install --save-dev ts-loader json-loader 
```

Enter fullscreen mode Exit fullscreen mode

## 插件

让我们暂时忽略这一点

## 解决

您可以在这里告诉 webpack 在传输过程中扫描哪些文件扩展名。我们添加了`.ts`和`.js`，因为我们想一次将一个 js 文件转换成 ts。这意味着，我们将在项目中混合使用 js 和 ts 文件，我们希望 webpack 同时考虑这两种文件

## 输出

这是我们告诉 webpack 我们希望 transpilation 的输出如何出现的地方。我们说我们希望输出文件以我们在`entry`部分中使用的文件名命名。我们希望将输出文件复制到当前目录下名为`lib`的文件夹中。我们希望 webpack 使用`commonjs`模块系统。

同样，如果这是您第一次使用 webpack，那么不要太担心这个文件的内容。这是一个最小的配置，只适用于任何服务器端代码。

接下来，我们需要一个 Typescript 配置。将名为`tsconfig.json`的文件添加到项目中。同样，该文件的最小内容如下

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "noImplicitAny":  true,  "lib":  [  "es5","es2015",  "es6",  "dom"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 Typescript 编译器，我们希望生成的 JS 符合 ES5，并且我们希望使用`commonjs`作为我们的模块系统。我们还添加了一个设置为`true`的`noImplicitAny`。这迫使你声明任何类型为`any`的变量，而不是省略类型声明，编译器将变量标记为`any`。这有助于捕捉我们忘记声明变量类型的情况。

接下来，我们需要一种调用 webpack 的方法。有两种方法可以做到这一点。我们之前安装的 webpack npm 包，您可以全局安装，只需从项目根目录下的控制台运行`webpack`。或者你可以在你的`package.json`中添加一个 NPM 脚本，它使用本地安装的 webpack 版本，如下图

```
"scripts":  {  "build":  "node_modules/.bin/webpack --config webpack.config.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

注意，我填充了一个并不真正需要的`--config`标志，因为 webpack 在默认情况下会查找一个名为`webpack.config.js`的文件。但是如果你喜欢用不同的名字命名你的 webpack 配置文件，那么确保你传递了`--config`标志。

本节末尾的源代码在 [add-webpack](https://github.com/schatekar/js-to-ts/tree/add-webpack) 分支中。

# 2。添加类型声明文件

我们需要找到第一个可以安全地转换为 Typescript 的模块。这通常是我们项目的入口模块。在我们的例子中，那将是`index.js`。为了在转换后的模块中使用 Typescript 的全部功能，我们需要为该模块所依赖的其他模块提供类型声明文件。
关于类型声明文件，我们需要理解两个概念。我假设你知道什么是类型声明文件，如果不知道，我会推荐你阅读[关于这个主题的官方指南](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)

1.  我们需要为任何外部模块显式安装类型声明文件。在我们的例子中，我们有一个名为`prompt-sync`的外部模块，我们需要为它安装类型声明文件
2.  对于我们自己的还没有转换成 Typescript 的模块，我们需要自己编写类型声明文件作为权宜之计，直到我们将该模块转换成 Typescript

## 为外部模块安装类型声明文件

大多数 NPM 包的类型声明文件已经由社区提供。我们可以运行下面的命令来为我们的`prompt-sync`包
安装类型声明文件

```
npm install --save-dev @types/prompt-sync 
```

Enter fullscreen mode Exit fullscreen mode

如果类型声明文件可用，它将被安装。否则，您会看到一个错误。您需要自己创建必要的类型声明文件。

## [T1】为自己的模块创建类型声明文件](#creating-type-declaration-files-for-own-modules)

模块的类型声明文件包含模块导出的位的接口、函数和类型声明。它们在扩展名为`d.ts`的文件中声明，并以模块名或`index.d.ts`命名。例如，我们刚刚安装的`prompt-sync`模块的类型声明文件被命名为`index.d.ts`，你可以在`node_modules/@types/prompt-sync`文件夹中找到它。这是 typescript 编译器在模块解析期间搜索的已知位置之一。你可以在 [typescript 手册](https://www.typescriptlang.org/docs/handbook/module-resolution.html)中阅读更多关于 Typescript 编译器遵循的模块解析过程。编译器解析模块的策略之一是在与导入模块相同的位置查找与模块名称匹配的类型声明文件。例如，如果我们导入如下的模块

```
import * as calc from './calculator' 
```

Enter fullscreen mode Exit fullscreen mode

然后，typescript 编译器将在当前目录中查找一个`calculator.ts`或`calculator.d.ts`文件。我们可以使用这种机制，通过创建一个类似下面的
的文件`calculator.d.ts`，将我们现有的`calculator.js`文件放在类型声明之后

```
declare module calculator {
    export function add(a :number, b :number): number
    export function subtract(a :number, b :number): number
    export function multiply(a :number, b :number): number
    export function divide(a :number, b :number): number
}
export = calculator; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，它公开了与我们的计算器模块相同的方法，但是有带注释的参数和类型为`number`的返回值。这个文件需要放在`calculator.js`旁边。

## 为外部模块创建类型声明文件

在这个例子中，我们没有任何没有类型声明文件的外部模块。但是如果你是这种情况，你可以结合以上两点的知识。首先构建自己的类型声明文件，并将其命名为`index.d.ts`。这只能包括您在代码中使用的外部模块的方法/接口。这个类型声明文件 file 需要保存在`node_modules/@types/{module_name}/`文件夹下

我从未亲自尝试过，所以不能保证可靠性，但这是社区定义的类型声明文件正在做的事情。本节末尾的源代码在 [add-types](https://github.com/schatekar/js-to-ts/tree/add-types) 分支中。

# 3。将入口模块转换为类型脚本

最后，我们准备将我们的第一个模块转换成 TypeScript。这一步其实没什么。将`index.js`重命名为`index.ts`，并开始在 typescript 中重写模块。如果您使用`import`语法来引入依赖模块，那么 TypeScript 编译器将会查看目标模块的类型声明文件，并且除了通常的 Javascript 编译器检查之外，还会强制执行类型检查。这是我转换后的`index.ts`文件看起来像

```
import * as p from "prompt-sync"
import * as calc from "./calculator"
let prompt = p();

function readInput() {
  console.log("Welcome to the calculator. Choose one of the following options");
  console.log("1\. add");
  console.log("2\. subtract");
  console.log("3\. multiply");
  console.log("4\. divide");
  console.log("5\. exit");

  var option = prompt(">> ");

  if (option !== "5") {
    console.log("Enter the first number");
    let a = parseInt(prompt(">> "));

    console.log("Enter the second number");
    let b = parseInt(prompt(">> "));

    let c;
    switch(option){
      case "1": {
        c = calc.add(a, b);
        console.log(`a + b = ${c}`);
      }
      break;

      case "2": {
        c = calc.subtract(a, b);
        console.log(`a - b = ${c}`);
      }
      break;

      case "3": {
        c = calc.multiply(a, b);
        console.log(`a * b = ${c}`);
      }
      break;

      case "4": {
        c = calc.divide(a, b);
        console.log(`a / b = ${c}`);
      }
      break;
    }    

    readInput();
  }
}

readInput();

console.log("Thank you for using calculator. Good Bye"); 
```

Enter fullscreen mode Exit fullscreen mode

Yeyy。我们将第一个模块从 javascript 转换为 typescript。如果此时运行`npn run build`，您会注意到 webpack 成功地在`lib/index.js`中给了我们一个打包的包，可以使用了。

本节末尾的源代码在 [convert-entry-module](https://github.com/schatekar/js-to-ts/tree/convert-entry-module) 分支中。

# 4。继续

转换第一个 javascript 文件是一大胜利。你现在已经有了基本的管道，可以承担更大的任务了。您可能想要扩展您的 webpack 配置，以包含您的项目中可能有的其他类型的文件，添加生产构建步骤，如缩小、丑化等。同时，你还需要不断地将越来越多的文件从 javascript 转换成 typescript。下一个合乎逻辑的步骤是通过将 javascript 模块转换成 typescript 来去掉我们自己的类型声明文件。我们把`calculator`模块改一下，去掉`calculator.d.ts`。有许多方法，你可以用 typescript 重写计算器模块。最简单的方法是像下面这样导出模块中的四个方法。

```
export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}
export function divide(a: number, b: number): number {
  return a / b;
} 
```

Enter fullscreen mode Exit fullscreen mode

删除`calculator.d.ts`文件并重新运行`npm run build`，您将在`lib/index.js`中获得您的打包包。

就是这样。我们已经将这个项目中的所有内容从 javascript 转换为 typescript。本节末尾的源代码在 [keep-going](https://github.com/schatekar/js-to-ts/tree/keep-going) 分支中。