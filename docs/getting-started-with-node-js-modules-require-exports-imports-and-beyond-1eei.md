# Node.js 模块入门:要求、导出、导入等等

> 原文：<https://dev.to/amejiarosario/getting-started-with-node-js-modules-require-exports-imports-and-beyond-1eei>

Node.js 模块入门:`require`、`exports`、`imports`等。

模块是理解 Node.js 项目的一个重要概念。在这篇文章中，我们将讨论节点模块:`require`、`exports`和未来的`import`。

节点模块允许您编写可重用的代码。你可以把它们一个套在另一个里面。使用节点包管理器(NPM)，您可以发布您的模块，并使它们对社区可用。此外，NPM 使您能够重用其他开发人员创建的模块。

> 我们使用 Node 12.x 作为示例和 ES6+语法。但是，这些概念对任何版本都有效。

在本节中，我们将介绍如何创建节点模块及其每个组件:

*   需要
*   电子竞技
*   模块(模块.导出与导出)
*   导入

# 要求

`require`用于消费模块。它允许你在程序中包含模块。可以添加内置的核心 Node.js 模块、基于社区的模块(`node_modules`)和本地模块。

假设我们想从文件系统中读取一个文件。节点有一个核心模块叫做‘fs’:

```
const fs = require('fs');

fs.readFile('./file.txt', 'utf-8', (err, data) => {
  if(err) { throw err; }
  console.log('data: ', data);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将“fs”模块导入到了代码中。它允许我们使用任何附属于它的功能，像“readFile”和许多其他功能。

`require`功能将按以下顺序查找文件:

1.  **内置**核心 Node.js 模块(如`fs`)
2.  **NPM 模块**。它会在`node_modules`文件夹中查找。
3.  **本地模块**。如果模块名有`./`、`/`或`../`，它将在给定的路径中寻找目录/文件。匹配文件扩展名:`*.js`、`*.json`、`*.mjs`、`*.cjs`、`*.wasm`和`*.node`。

现在让我们更详细地解释一下

## 内置模块

当您安装 node 时，它带有许多内置模块。节点附带电池；)

一些最常用的核心模块是:

*   [fs](https://nodejs.org/api/fs.html) :允许你操作(创建/读/写)文件和目录。
*   [路径](https://nodejs.org/api/path.html):处理文件和目录路径的工具。
*   [http](https://nodejs.org/api/http.html) :为 web 开发创建 http 服务器和客户端。
*   [url](https://nodejs.org/api/url.html) :解析 url 并从中提取元素的工具。

这些你不必安装它，你可以导入它们并在你的程序中使用它们。

## NPM 模块

NPM 模块是第三方模块，安装后即可使用。仅举几个例子:

*   [lodash](https://www.npmjs.com/package/lodash) :操作数组、对象和字符串的实用函数集合。
*   [请求](https://www.npmjs.com/package/request):比内置`http`模块简单易用的 HTTP 客户端。
*   [express](https://www.npmjs.com/package/express) :搭建网站和 API 的 HTTP 服务器。同样，比内置的`http`模块更容易使用。

这些你要先安装，像这样:

```
npm install express 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以像引用内置模块一样引用它们，但这次它们将从包含所有第三方库的`node_modules`文件夹中提供。

```
const express = require('express'); 
```

Enter fullscreen mode Exit fullscreen mode

## 创建自己的 Nodejs 模块

如果你找不到你想要的内置或第三方库，你将不得不自己开发。
在接下来的章节中，您将学习如何使用`exports`来完成这项工作。

# 出口

关键字`exports`让你有机会“导出”你的对象和方法。我们来做一个例子:

```
const PI = 3.14159265359;

exports.area = radius => (radius ** 2) * PI;
exports.circumference = radius => 2 * radius * PI; 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码中，我们导出了`area`和`circumference`函数。我们定义了`PI`常量，但这只能在模块内部访问。只有与`exports`相关的元素在模块外部可用。

因此，我们可以在另一个文件中使用`require`来消费它，如下所示:

```
const circle = require('./circle');

const r = 3;
console.log(`Circle with radius ${r} has
  area: ${circle.area(r)};
  circumference: ${circle.circumference(r)}`); 
```

Enter fullscreen mode Exit fullscreen mode

注意，这次我们在模块名前面加上了前缀`./`。指示该模块是本地文件。

# 模块包装器

您可以将每个 Node.js 模块视为一个独立的函数，如下所示:

模块包装:

```
(function (exports, require, module, __filename, __dirname) {
  module.exports = exports = {};

  // Your module code ...

}); 
```

Enter fullscreen mode Exit fullscreen mode

我们已经讲过了`exports`和`require`。注意`module.exports`和`exports`之间的关系。它们指向同一个参考。但是，如果你直接给`exports`分配东西，你将会断开它与`module.exports`的链接——下一节将会详细介绍。

为了方便起见，定义了`__filename`和`__dirname`。它们提供了当前文件和目录的完整路径。后者不包括文件名，并打印出目录路径。

例如，对于我们的`./circle.js`模块，应该是这样的:

*   `__filename` : `/User/adrian/code/circle.js`

*   `__dirname` : `/User/adrian/code`

好了，我们已经讲了`exports`、`require`、`__filename`和`__dirname`。我们唯一没有报道的是`module`。让我们去吧！

# 模块导出与导出

`module`不是全局的；它对于每个模块都是本地的。它包含关于模块的元数据，如 id、导出、父、子等。

`exports`是`module.exports`的别名。因此，无论你分配给`exports`什么，在`module.exports`上也是可用的。但是，如果你直接把某样东西分配给出口，那么你就失去了通往`module.exports`的捷径。例如

```
class Cat {
  makeSound() {
    return `${this.constructor.name}: Meowww`;
  }
}

// exports = Cat; // It will not work with `new Cat();`
// exports.Cat = Cat; // It will require `new Cat.Cat();` to work (yuck!)
module.exports = Cat; 
```

Enter fullscreen mode Exit fullscreen mode

用`exports`然后用`module.exports`尝试以下情况。

```
const Cat = require('./cat');

const cat = new Cat();
console.log(cat.makeSound()); 
```

Enter fullscreen mode Exit fullscreen mode

总结一下，什么时候用`module.exports` vs `exports`:

使用`exports`来:

*   导出命名函数。如`exports.area`、`exports.circumference`。

使用`module.exports`来:

1.  如果您想在根级别导出一个对象、类、函数(如`module.exports = Cat`)

2.  如果您希望返回一个公开多个赋值的对象。例如`module.exports = {area, circumference};`

# 进口

从 8.5.0+版本开始，Node.js 通过一个特性标志和新的文件扩展名`*.mjs`原生支持 ES 模块。

例如，我们之前的`circle.js`可以重写为`circle.mjs`，如下所示:

circle.mjs

```
const PI = 3.14159265359;

export function area(radius) {
  return (radius ** 2) * PI;
}

export function circumference(radius) {
  return 2 * radius * PI;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用导入:

main . mj〔t0〕

```
import { area, circumference } from './circle.mjs';

const r = 3;

console.log(`Circle with radius ${r} has
  area: ${area(r)};
  circunference: ${circumference(r)}`); 
```

Enter fullscreen mode Exit fullscreen mode

最后，您可以使用实验模块特性标志:
来运行它

```
node --experimental-modules main.mjs 
```

Enter fullscreen mode Exit fullscreen mode

如果你不喜欢实验模块，另一个选择是使用 transpiler。为您将现代 JavaScript 转换为旧版本。好的选项是[打字稿](https://www.typescriptlang.org/docs/handbook/modules.html)、[巴别塔](https://babeljs.io/docs/en/babel-plugin-transform-modules-commonjs)和[汇总](https://rollupjs.org/guide/en#importing)。

## 故障排除`import`和`require`问题

### 实验旗

如果你不使用实验标志`node --experimental-modules`而试图使用`import`，你会得到这样的错误:

```
internal/modules/cjs/loader.js:819
  throw new ERR_REQUIRE_ESM(filename);
  ^

Error [ERR_REQUIRE_ESM]: Must use import to load ES Module: bla bla blah 
```

Enter fullscreen mode Exit fullscreen mode

### 文件扩展名。mjs vs .js(或者。cjs)

如果你有一个`*.mjs`文件，你不能使用`require`，否则它会抛出一个错误(`ReferenceError: require is not defined`)。
`.mjs`用于`import` ECMAScript 模块，`.js`用于常规`require`模块。

然而，使用`*.mjs`你可以加载这两种模块！

```
import { area, circumference } from './circle.mjs';
import Cat from './cat.js';

const r = 3;
console.log(`Circle with radius ${r} has
  area: ${area(r)};
  circumference: ${circumference(r)}`);

const cat = new Cat();
console.log(cat.makeSound()); 
```

Enter fullscreen mode Exit fullscreen mode

注意`cat.js`正在使用 commonJS 模块。

# 总结

我们学习了如何创建 Node.js 模块，并在代码中使用了它。模块允许我们轻松地重用代码。它们提供独立于其他模块的功能。`require`功能用于加载模块。`exports`和`module.exports`允许我们定义我们想要暴露的代码部分。我们还探索了`module.exports`和`exports`的区别。最后，我们快速挑选了使用`imports`的模块。