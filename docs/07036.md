# 学习 Webpack 的阶段。3 -源地图、加载器和插件

> 原文：<https://dev.to/nitishdayal/stages-of-learning-webpack-pt-3---sourcemaps-loaders--plugins>

## [T1】简介](#intro)

本文是学习 Webpack 系列的[阶段的延续。自上一篇文章以来，包含源代码的存储库已经过更新，包括了所有依赖项的最新版本以及一些配置改进(阅读:我第一次可能犯了很多错误)。](https://dev.to/nitishdayal/stages-of-learning-webpack)

[Github 回购](https://github.com/nitishdayal/webpack-stages-example)

### 第二步，续。

在某些时候，您需要调试您的应用程序。大多数现代浏览器都提供直观的*开发工具*，可以帮助你调试和优化应用程序。

在文本编辑器中，将`src/app/sayHello.js`中的第一行改为:

```
export default name => alet(`Hello ${name}`); 
```

Enter fullscreen mode Exit fullscreen mode

我们在应用程序中故意将`alert`拼错为`alet`，从而犯了一个错误。从命令行运行`npm run build` / `yarn build`。构建应该仍然成功；Webpack 不负责维护我们的 JavaScript 代码的准确性。在浏览器中打开`index.html`文件，并打开浏览器的开发工具。将会出现一条类似于`ReferenceError: alet is not defined`的错误信息。

单击错误消息右侧的文件名，将会导航到发生错误的文件的行和列。由于我们的`index.html`文件使用生成的 Webpack 包来加载我们的 JavaScript 代码，我们将被指向生成的 Webpack 包中发生错误的那一行。从那里开始，由我们从*包*中的错误点回溯到我们实际源代码中的错误点。

如果这就是调试所涉及的内容，那么我就不再调试了。一定有更简单的方法。我们可以做得更好。 ***我们有技术。*T3】**

### 第三步

*分支:sourceMaps*

让我们先来看看这个分支(sourceMaps)和前一个分支(init)之间的区别:

* * *

#### 修改为`webpack.config.js`:

我们已经向 Webpack 配置对象添加了一个新的密钥；`devtool`键。与该键相关联的值取决于*参数* `env`的值。

```
module.exports = env => ({
  devtool: (env && env.production) ? "source-map" : "cheap-eval-source-map",
  entry: "./src/index.js",
  output: { filename: "./build/bundle.js" },
  resolve: { extensions: [".js"] }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将 ES5 中的文件重写为:

```
module.exports = function(env) {
  var devtool;

  if (env !== undefined && env.production === true) {
      devtool = "source-map";
  } else {
      devtool = "cheap-eval-source-map";
  };

  return {
    devtool: devtool,
    entry: "./src/index.js",
    output: { filename: "./build/bundle.js" },
    resolve: { extensions: [".js"] }
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们声明一个变量`devtool`。然后，*如果*的`env`参数不是`undefined` **并且** `env`是一个具有键/值对`{ production: true }`的对象，那么将`devtool`的值定义为一个字符串`"source-map"`。否则将`devtool`定义为`"cheap-eval-source-map"`。与这些值相关的含义将在后面解释；现在，我想澄清一下，我们所做的只是创建一个变量，并将该变量定义为一个字符串。该字符串的值取决于条件语句(if/else 块*)。*

最后，我们返回一个带有一组键/值对的对象，Webpack 可以用它来创建我们的包。`entry`、`output`和`resolve`键/值对已经从`init`分支转移过来。

#### 修改为`package.json`:

我们已经更新了`package.json`文件的`scripts`部分。

**之前:**

```
/*...*/
  "scripts": {
    "build": "webpack"
  },
/*...*/ 
```

Enter fullscreen mode Exit fullscreen mode

**之后:**T2】

```
/*...*/
  "scripts": {
    "dev": "webpack",
    "prod": "webpack --env.production"
  },
/*...*/ 
```

Enter fullscreen mode Exit fullscreen mode

调用 Webpack 的命令名称已从`build`更改为`dev`。命名约定暗示这将创建一个*开发*版本的包，这是真的。我们没有让 Webpack 在创建包时运行任何优化。我们的配置只是说‘获取这个入口文件(`src/index.js`)和它导入的每个文件，将它们捆绑在一起，并将那个捆绑包输出为一个文件(`./build/bundle.js`)。

还有一个新键，`prod`。同样，命名约定意味着这将创建一个捆绑包的*生产*版本。并没有。还没有。但是会的！现在，`prod`脚本和`dev`脚本之间的唯一区别是，我们现在将一个参数作为`env`参数传递给`webpack.config.js`中导出的函数，然后函数使用它来创建和返回 Webpack 配置对象。要看到这一点，您可以在从`webpack.config.js`导出的函数中放置一个`console.log(env)`语句。

```
// webpack.config.js
module.exports = env => {
  console.log(env);

  return {
    devtool: env && env.production ? "source-map" : "cheap-eval-source-map",
    entry: "./src/index.js",
    output: { filename: "./build/bundle.js" },
    resolve: { extensions: [".js"] }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

从命令行运行命令`npm run dev` / `yarn dev`。

```
> webpack

undefined
Hash: 9d81a1b766e4629aec0c
Version: webpack 2.6.1
Time: 82ms
            Asset     Size  Chunks             Chunk Names
./build/bundle.js  5.75 kB       0  [emitted]  main
   [0] ./src/app/sayHello.js 233 bytes {0} [built]
   [1] ./src/index.js 453 bytes {0} [built] 
```

Enter fullscreen mode Exit fullscreen mode

紧接在 T1 之后的 T0 是我们的 T2 语句。它是未定义的，因为在我们的`dev`命令中，我们没有向 Webpack 传递任何额外的参数。现在，让我们从命令行运行`npm run prod` / `yarn prod`命令。

```
> webpack --env.production

{ production: true }
Hash: cbc8e27e9f167ab0bc36
Version: webpack 2.6.1
Time: 90ms
                Asset     Size  Chunks             Chunk Names
    ./build/bundle.js  3.79 kB       0  [emitted]  main
./build/bundle.js.map  3.81 kB       0  [emitted]  main
   [0] ./src/app/sayHello.js 233 bytes {0} [built]
   [1] ./src/index.js 453 bytes {0} [built] 
```

Enter fullscreen mode Exit fullscreen mode

我们看到的不是`undefined`，而是具有一个键/值对`{ production: true }`的对象。这些值与我们的 Webpack 配置中的条件语句相匹配；我们的条件语句确保参数`env`不是未定义的，并且它是一个具有键/值对`{ production: true }`的对象。您可能已经注意到，这些命令生成的包也是不同的。用`dev`命令生成的包比用`prod`生成的包大，但是`prod`命令生成了一个附加文件`bundle.js.map`。

* * *

打开文件`src/app/sayHello.js`。由于这是 Git 存储库的一个不同的分支，如果在`init`分支中进行了更改，我们之前放在这个文件中的错误可能不会继续存在。如果是这种情况，修改第一行，这样`alert`调用就会被拼错为`alet`。保存您的更改，然后再次从命令行运行`npm run dev/yarn dev`。在浏览器中打开`index.html`，然后打开浏览器的 devtools。您应该会在控制台中看到一个错误消息`alet is not defined`。

如果控制台声称这个错误是在`index.html`文件中生成的，请刷新页面。您应该会看到如下内容:

```
ReferenceError: alet is not defined          sayHello.js?7eb0:1 
```

Enter fullscreen mode Exit fullscreen mode

单击这个错误应该会将您带到发生错误的行&文件，但是您会注意到整行都被突出显示为错误。在这种情况下，这并不完全不准确。但是假设我们再次改变了`src/app/sayHello.js`文件。这一次，我们将把`alert`调用中对`name`的引用改为`namen` :

```
export default name => alert(`Hello ${namen}`);

export const donut = "I WANT YOUR DONUTS";

/**
 * Same code, ES5 style:
 * 
 * function sayName(name){
 *    return alert('Hello ' + name);
 * }
 * 
 * export default sayName;
 * 
 */ 
```

Enter fullscreen mode Exit fullscreen mode

再次从命令行运行`npm run dev/yarn dev`，刷新浏览器中打开的`index.html`文件。您的 devtools 中的控制台应该显示类似的错误消息；`namen is not defined`。单击错误消息将再次把我们带到发生错误的那一行。

现在，从命令行运行`npm run prod` / `yarn prod`，刷新浏览器中的`index.html`文件。打开您的 devtools，在您的控制台中查看错误，文件名现在只是`sayHello.js`。点击错误不仅会导航到发生错误的文件&行，还会导航到发生错误的列。错误下划线也更具体；它从`namen`开始，而不是在整个第一行下划线。

这就是两个命令的区别。他们生成的源地图的准确性。我们出于开发目的使用不太精确版本的源地图的原因是因为它们比每次我们创建一个构建时让 Webpack 生成完整的源地图文件更快。您可以在这里了解 Webpack 用于源映射的不同选项: [Webpack Devtool 配置。](https://webpack.js.org/configuration/devtool/)

### 第四步

*分支:加载器*

请注意，生成的包保留了源文件中使用的所有 ES2015 语法；`let` & `const`、箭头函数、较新的对象文字语法等。如果我们试图在不支持这些特性的旧浏览器中运行我们的应用程序，应用程序将会失败。这就是我们通常利用 *transpiler* 的地方，比如 Babel、TypeScript、CoffeeScript 等。运行我们的代码，并将其翻译成具有更好的跨浏览器支持的版本。*加载器*分支涵盖了如何将 TypeScript 集成到我们的 Webpack 构建过程中，以便将我们的应用程序代码向下转换为 ES3 语法。注意，我们没有引入任何特定于 TypeScript 的特性；我甚至把文件当成。js 文件。我们将使用 TypeScript 作为 ESNext - > ES3 传输程序。

给人们系上安全带；这次会很颠簸。

#### 依赖关系

查看`package.json`文件，我们添加了两个新的*开发者依赖关系*。

*   TypeScript:如前所述，我们将使用 TypeScript 作为 transpiler。
*   TS-Loader:加载器让 Webpack 比 JavaScript 更能理解*。在这种情况下，TS-Loader 允许 Webpack 使用 TypeScript 来加载 TypeScript(和 JavaScript)文件，并在生成浏览器友好的包之前根据您的 TypeScript 配置来转换它们。*

要安装这些依赖项，请从命令行运行`npm install`。NPM 应该读取`package.json`文件并安装列出的依赖项。一般来说，要安装额外的开发者依赖项，可以运行`npm i -D <package-name>`，其中`<package-name>`是你要安装的包，即:`npm i -D typescript`。D 标志告诉 NPM 将安装包保存为开发者依赖项。

`prod`命令也被更新；它现在包含了标志`-p`。`-p`标志是一个可以提供给 Webpack CLI(命令行界面，当`package.json`文件中的`script`使用`webpack`时 NPM 调用的工具)的选项，它为生产环境提供优化。我们将很快对此进行更深入的研究。

#### 打字稿配置

`tsconfig.json`文件为 TypeScript 提供了在编译我们的代码时使用的信息。

```
{
  "compilerOptions": {
    "allowJs": true,
    "module": "es2015",
    "target": "es3",
    "sourceMap": true,
    "strict": true
  },
  "include": [
    "./src/"
  ],
  "exclude": [
    "node_modules/",
    "./build/"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这个配置对象告诉 TypeScript 一些事情:

*   TypeScript 一般用于将 TypeScript 文件(`.ts`)转换成 JavaScript。通过将`allowJs`设置为`true`，我们允许 TypeScript 转换文件。js 文件。
*   TypeScript 能够将 JavaScript 移植到各种模块系统中。我们告诉 TypeScript 使用 ES2015 模块系统，因为当使用这种变体创建应用程序时，Webpack 能够应用一些非常漂亮的优化。
*   我们可以针对从 ES3 到 ESNext 的大多数 JavaScript 版本。鉴于我们的目标是更好的浏览器支持，而不是更糟，我们选择 ES3。
*   为每个传输文件生成源映射。
*   使用 TypeScript 提供的所有类型检查特性。

#### Webpack 配置更新

```
module: {
  devtool: env && env.production ? "source-map" : "inline-source-map",
  /* ... */
  rules: [
    {
      test: /\.js(x)?/,
      loader: "ts-loader",
      options: {
        transpileOnly: true,
        entryFileIsJs: true
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们为 Webpack 配置对象引入了一个新的键；`module`。`module`部分向 Webpack 提供了关于如何处理应用程序中使用的某些文件的信息。我们提供了一个规则，可以这样理解:

> 当 Webpack 遇到以`.js`或`.jsx`结尾的文件时，它会先将文件传递给 TS-Loader，然后再生成请求的包。TS-Loader 只负责传输应用程序；我们没有使用 TypeScript 提供的任何类型检查功能。

用于开发环境的源映射类型已从“廉价评估源映射”更改为“内联源映射”。Webpack 文档中介绍了这两个选项之间的区别:这里: [Webpack Devtool 配置。](https://webpack.js.org/configuration/devtool/)

从命令行运行`npm run dev` / `yarn dev`，在浏览器中打开`index.html`文件。一切都应该按预期运行。查看生成的包中的第 73-105 行:

```
"use strict";
/* unused harmony export donut */
/* harmony default export */ __webpack_exports__["a"] = (function (name) { return alert("Hello " + name); });;
var donut = "I WANT YOUR DONUTS";
/**
 * Same code, ES5 style:
 *
 * function sayName(name){
 *    return alert('Hello ' + name);
 * }
 *
 * export default sayName;
 *
 */

/***/ }),
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
/* harmony import */ var __WEBPACK_IMPORTED_MODULE_0__app_sayHello__ = __webpack_require__(0);

// Import whatever the default export is from /app/sayHello
// and refer to it in this file as 'Hello'
var name = "Nitish";
// Reference to the <div id="root"> element in
var root = document.getElementById("root");
// Call the function that was imported from /app/sayHello, passing in
// `const name` that was created on line 5.
__webpack_require__.i(__WEBPACK_IMPORTED_MODULE_0__app_sayHello__["a" /* default */])(name);
root.textContent = "Helllloooo " + name + "!"; 
```

Enter fullscreen mode Exit fullscreen mode

所有的`const`和`let`声明已经被转换为`var`。警报消息和`root.textContent`中使用的模板字符串已被替换为字符串连接。我们的包是使用 TypeScript 生成的 *transpiled* 代码创建的。

如果我们记得从更早的时候起，`src/app/sayHello.js`出口了两个物品；一个函数作为*默认导出*，一个常量`donut`作为*命名导出*。

```
export default name => alert(`Hello ${name}`);

export const donut = "I WANT YOUR DONUTS"; 
```

Enter fullscreen mode Exit fullscreen mode

第二个导出没有在应用程序中的任何地方使用，但是它仍然包含在包中。然而，如果我们运行`npm run prod` / `yarn prod`并查看我们的包...

真是热的一塌糊涂！下面是这个包的(更好的、格式化的)外观:

```
!(function(t) {
  function e(r) {
    if (n[r]) return n[r].exports;
    var o = (n[r] = { i: r, l: !1, exports: {} });
    return t[r].call(o.exports, o, o.exports, e), (o.l = !0), o.exports;
  }
  var n = {};
  (e.m = t), (e.c = n), (e.i = function(t) {
    return t;
  }), (e.d = function(t, n, r) {
    e.o(t, n) ||
      Object.defineProperty(t, n, { configurable: !1, enumerable: !0, get: r });
  }), (e.n = function(t) {
    var n = t && t.__esModule
      ? function() {
          return t.default;
        }
      : function() {
          return t;
        };
    return e.d(n, "a", n), n;
  }), (e.o = function(t, e) {
    return Object.prototype.hasOwnProperty.call(t, e);
  }), (e.p = ""), e((e.s = 1));
})([
  function(t, e, n) {
    "use strict";
    e.a = function(t) {
      return alert("Hello " + t);
    };
  },
  function(t, e, n) {
    "use strict";
    Object.defineProperty(e, "__esModule", { value: !0 });
    var r = n(0), o = document.getElementById("root");
    n.i(r.a)("Nitish"), (o.textContent = "Helllloooo Nitish!");
  }
]);
//# sourceMappingURL=bundle.js.map 
```

Enter fullscreen mode Exit fullscreen mode

还是热的一塌糊涂！不太需要手动解析它；这是生命善的 38 条线，所以这是可行的，但是没有义务，它不会对本指南的其余部分有所帮助。我在这里试图展示的是，生成的*生产*包没有引用线`const donut = "I WANT YOUR DONUTS!";`。它完全从包里掉了出来。当提供了`-p`标志时，**摇树**是该列表的一部分，随着缩小、丑化和少数其他开箱即用的生产优化，Webpack 能够实现。我不需要做任何事情来启动摇树。它就是有效。

太棒了。我们正在将我们的 ES2015+代码向下传输到 ES3，删除任何未使用的代码，并生成一个生产(ish)质量的捆绑包，可以由大多数现代浏览器加载，错误和警告指向我们的源代码，以简化调试。

### 第五步

*分支:插件*

插件完全按照它们在标签上说的做；它们插入到构建过程中来引入额外的功能。在本例中，我们将了解到 [HTMLWebpackPlugin](https://github.com/jantimon/html-webpack-plugin#configuration) ，这是一个用于生成 HTML 文档的插件，可以为我们的 Webpack 包提供服务。

现在，我们创建了一个指向预期包的 HTML 文件。在简单的情况下，像这样的设置会工作得很好。随着应用程序的增长，包可能会被分割成多个文件，文件名可能是随机生成的，等等。如果我们试图手动维护需要加载到 HTML 文件中的文件列表...我们又回到起点了，对吧？我们将使用 HTMLWebpackPlugin 来自动化将包加载到 HTML 文档中的过程。

#### 文件修改

1.  向`package.json`文件引入了一个新的开发者依赖项；`HTMLWebpackPlugin`。当您切换到这个分支时，确保运行`npm install` / `yarn`来获得必要的依赖项。

```
 "devDependencies": {
      "html-webpack-plugin": "^2.28.0",
      "ts-loader": "^2.1.0",
      "typescript": "^2.3.4",
      "webpack": "^2.6.1"
    } 
```

Enter fullscreen mode Exit fullscreen mode

1.  `index.html`文件不再加载`build/bundle.js`文件。

2.  `webpack.config.js`已更新，在文件顶部包含一个 CommonJS 样式的导入语句(`const HTMLWebpackPlugin = require("html-webpack-plugin");`)，并有一个新的部分，`plugins` :

```
 //webpack.config.js
    const HTMLWebpackPlugin = require("html-webpack-plugin");

    module.exports = env => {
      /*...*/
      plugins: [
        new HTMLWebpackPlugin({
          filename: "./build/index.html",
          inject: "body",
          template: "./index.html"
        })
      ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们告诉 Webpack，我们将使用 HTMLWebpackPlugin 在`build`文件夹中生成一个名为`index.html`的 HTML 文件。HTMLWebpackPlugin 获取任何生成的包并`inject`将它们放入脚本标签中的 HTML 文件体中。它将使用我们的应用程序根中现有的`index.html`作为模板。

如果我们调用`npm run dev` / `yarn dev`或`npm run prod` / `yard prod`，我们应该看到类似于
的东西

```
$ npm run dev

> webpack -p --env.production

ts-loader: Using typescript@2.3.4 and /Projects/dev_to/webpack_configs/example/tsconfig.json
Hash: 693b4a366ee89bdb9cde
Version: webpack 2.6.1
Time: 2233ms
             Asset       Size  Chunks             Chunk Names
 ./build/bundle.js    8.96 kB       0  [emitted]  main
./build/index.html  352 bytes          [emitted] 
```

Enter fullscreen mode Exit fullscreen mode

基于所提供的配置，Webpack 生成了所请求的包以及一个`index.html`文件。生成的`index.html`文件看起来与我们现有的模板非常相似，但是在文档体中包含了对生成的 Webpack 包的引用。

在你的浏览器中打开**新的**index.html 文件(`./build/index.html`)，以确保一切按预期运行。

现在退后一步，享受你的工作，沉浸其中。朋友们，你们正在打包环游世界的路上。