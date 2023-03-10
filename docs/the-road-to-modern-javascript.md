# 现代 JavaScript 之路

> 原文：<https://dev.to/ruidfigueiredo/the-road-to-modern-javascript>

当我最近决定学习 [webpack](https://webpack.github.io/) 时，我意识到在过去几年中 JavaScript 生态系统中加入了多少新东西。如果你想对 Angular、React、Gulp、Webpack 等新框架和工具感到舒适，你需要知道的事情。

这篇博文的目标是带您了解这种语言的主要发展，这些发展导致了现代 JavaScript 的发展。它还举例说明了现代工具和功能是如何工作的。

[![Road to Modern Javascript symbolized by a curvy road](img/79fa3e733300a1c2cf02566b370cb3fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BJwT2Qfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ov5s2ymtwhrbmp6b9pnp.jpg)

在过去的二十年里，JavaScript 开发发生了巨大的变化。当 JavaScript 在 1995 年第一次被引入时，一个主要的目标是它应该对初学者来说是容易的。它有一些要求，比如可以直接嵌入 HTML。它应该是 Java 小程序之间的“粘合剂”。

我们都知道它朝着一个非常不同的方向发展。所有这些都是因为 JavaScript 在 web 开发中扮演了越来越重要的角色，而这与一些早期的目标相冲突。

## 范围和命名

在 90 年代，在一个`.html`文件中发现这一点是很常见的:

```
<input type="button" value="Save" onclick="save();"/>
<script>
  function save() {
    //...
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

脚本标签包含大量与 HTML 混合的代码，以及内联事件处理程序。所有这些很快使得代码难以阅读和维护。

另一个导致问题的原因是，很容易出现意外重定义函数的情况，因为你用和以前一样的方式命名了它。

例如，如果有两个定义了一个`save`函数的`.js`文件，那么第二个文件将覆盖第一个文件。这在 JavaScript 中完全有效，所以不会有错误或警告消息。

这个问题的解决方案是试图模仿其他编程语言中存在的`namespace`功能。我们开始做这样的事情:

```
var MyNamespace = (function() {
  function save(){
    //...
  }

  return {
    save: save
  };
})() 
```

Enter fullscreen mode Exit fullscreen mode

然后我们不再只调用`save()`，而是调用`MyNamespace.save()`。

这利用了 JavaScript 中新的作用域只能由函数创建的事实。这变得如此流行，以至于 IIFE 成了 JavaScript 开发人员之间的常用“词”(iffy)。表示[立即调用的函数表达式](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)。一个非常简单的例子是:

```
(function() { 
    //whatever variables and functions you declare here won't be "visible" outside the function
})() 
```

Enter fullscreen mode Exit fullscreen mode

现在有可能有更复杂的应用程序，并重用部分代码，因为函数的命名不是问题。

我们也开始让我们的 JavaScript 变得“不引人注目”，这意味着我们没有将它与 HMTL 混合，我们[变得更加面向对象](https://developer.mozilla.org/ms/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)。

## 要加载的文件太多

随着这些新的实践使得编写更复杂的 JavaScript 变得更容易管理，我们开始进入我们拥有大量 JavaScript 的情况。JavaScript 必须被加载到浏览器中，并且按照良好实践的要求，它必须被分离到几个具有有意义名称的文件中。

嗯，一个浏览器可以处理的并发 GET 请求的数量是有限制的，它们是[而不是](http://www.browserscope.org/?category=network)。

我们已经开始使用工具*捆绑*我们所有的 JavaScript。捆绑意味着所有的 JavaScript 代码被连接成一个文件。我第一次体验[捆绑是在 ASP.NET](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification)的时候。和 ASP.NET 在一起实际上。捆绑 JavaScript 文件的. Net 代码。

这只适用于。因此，为了将这种技术与其他技术一起使用，需要有替代方案。

在某个时候，有人认为让 JavaScript 在浏览器之外运行是个好主意。 [Node.js](https://nodejs.org) 被创建。Node 利用了谷歌创建的开源 [V8 JavaScript 引擎](https://developers.google.com/v8/)。Node 最吸引人的地方在于，你可以创建 [C++插件](https://nodejs.org/api/addons.html)，这些插件可以通过运行在 Node 中的 JavaScript 调用，这基本上意味着你没有在浏览器中运行的任何限制(可以访问文件系统等)。

许多使用 Node 创建的工具开始出现。具体做*捆绑*最流行的是[咕噜](https://gruntjs.com/)和[大口](http://gulpjs.com/)。

实际上，Grunt 和 Gulp 是任务运行程序，这意味着它们运行任务，而捆绑只是这些可能的任务之一。另一个与捆绑密切相关的例子是。网世界)。这是通过将变量名和函数名重命名为单个字母，并删除所有空格和注释，使 JavaScript 尽可能小的过程。

下面是创建包的 gulp 配置文件的一个示例:

```
var gulp = require('gulp');
var concat = require('gulp-concat');

gulp.task('default', function(){
  gulp.src(['player.js', 'game.js'])
      .pipe(concat('bundle.js'))
      .pipe(gulp.dest("."));
}); 
```

Enter fullscreen mode Exit fullscreen mode

当你用 gulp 运行这个任务时，它会用 *player.js* 和 *game.js* 创建一个名为 *bundle.js* 的包。如果你对学习 Gulp 感兴趣，我推荐:[用 Gulp.js](https://scotch.io/tutorials/automate-your-tasks-easily-with-gulp-js) 轻松自动化你的任务。

## 模块

尽管捆绑解决了浏览器可以同时执行的 GET 请求数量有限的问题，但是如果 JavaScript 文件之间存在依赖关系，则需要按照特定的顺序将它们添加到捆绑包中。还很容易出现这样的情况，即有 JavaScript 代码永远不会在包中执行。随着时间的推移，包变得难以管理。

JavaScript 模块解决了这个问题。使用模块背后的想法是有可能显式地声明依赖关系。例如，假设您正在创建一个 JavaScript 游戏，并且您有一个`game.js`文件。该文件使用了另一个名为`player.js`的文件中的代码。我们可以明确地说，`game.js`依赖于`player.js`。

有几种不同的模块“格式”。最常见的有 Node.js 中使用的 [commonjs](http://wiki.commonjs.org/wiki/Modules/1.1.1) ，还有异步模块定义(AMD)】(【https://github.com/amdjs/amdjs-api/wiki/AMD】)和 [ES6 模块](http://exploringjs.com/es6/ch_modules.html#sec_basics-of-es6-modules)。

让我们用`game.js`和`player.js`想象一个简单的场景，并用这三种模块格式来描述它们。游戏有一个调用玩家的`getName`方法的`start`方法。

在所有这些模块格式中，每个 JavaScript 文件都是一个模块，所以在这种情况下，我们有两个模块，游戏和玩家。

### CommonJS

有了*commonjs*,*player . js*文件看起来会像这样:

```
var privateVar; //if this is not "exported" it won't be available outside player.js

function getName() {
  //...
}

module.exports.getName = getName; 
```

Enter fullscreen mode Exit fullscreen mode

以及 *game.js* :

```
var player = require('./player.js');

function start(){
  var playerName = player.getName();
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过`module.exports`向任何请求模块的人公开模块内部的内容。在这种情况下，唯一被“导出”的是`getName`函数。

在 *commonjs* 中，我们使用 *require* 函数来获取另一个模块的导出零件。您可能已经注意到了`game.js`中 require 语句中的`./`。在这种情况下，这意味着两个文件在同一个文件夹中，但是模块文件的查找方式可能会变得复杂。我建议阅读 [Node.js 文档，了解如何在使用 require](https://nodejs.org/api/modules.html#modules_all_together)时获得确切的文件名。

### 异步模块定义

AMD 的语法有一点不同，它包括使用一个 *define* 函数，其中一个模块的依赖关系在一个数组中列出，然后提供一个函数，其中每个参数都是一个依赖关系，按照它们在数组中列出的顺序。

有了 AMD 的 *player.js* 会是这样的:

```
define([], function(){
  var privateVar; //not accessible outside the module

  function getName() {
    //...
  }
  return {
    getName: getName
  };
}) 
```

Enter fullscreen mode Exit fullscreen mode

以及 *game.js* :

```
define(['./player'], function(player) {
  function start(){
    var playerName = player.getName();
    //...
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个很好的资源来学习更多关于[和](http://requirejs.org/docs/whyamd.html)的知识。

### ES6 模块

ECMAScript 6 标准是 JavaScript 的新规范(可以称之为 JavaScript 的新版本),它引入了模块。

对于 ES6 模块， *player.js* 文件将如下所示:

```
var privateVar;

function getName(){
  //...
}

export { getName }; 
```

Enter fullscreen mode Exit fullscreen mode

而 *game.js* 会是这样的:

```
import * as player from './player.js'

function start() {
  var playerName = player.getName();
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 模块加载器

如果你只是加载上面例子中定义的 *game.js* 或 *player.js* ，它们就不会工作(你会得到错误消息，指出 require/define/import 没有定义)。

为了让它们工作，需要通过模块加载器加载它们。模块加载器是在浏览器中运行的 JavaScript 库，能够解释一种(或几种)模块格式。

有几种流行的模块加载器。最受欢迎的大概就是 [SystemJS](https://github.com/systemjs/systemjs) 了。

SystemJS 支持[几种模块格式](https://github.com/systemjs/systemjs/blob/master/docs/module-formats.md)。您可以通过配置选项指定您正在使用哪一个。

要使用它们，您需要指定哪个模块是“入口点”。您可以将入口点视为主模块，在我们的示例中是 game。

下面是我们如何使用 *SystemJS* 来加载上面的 *CommonJS* 示例:

```
<script src="system.js"></script>
<script>
  SystemJS.config({
    meta: {
      format: "cjs" //use commonjs module format
    }
  });

  SystemJS.import('game.js');
</script> 
```

Enter fullscreen mode Exit fullscreen mode

当你这样做的时候 *SystemJS* 会加载 *game.js* 检查它并意识到它需要获取 *player.js* 。然后，它将从浏览器中的 *player.js* 和 *game.js* 加载 JavaScript。

你可以在这个 pluralsight 课程中找到关于 JavaScript 模块和模块加载器的很好的介绍: [JavaScript 模块基础](https://www.pluralsight.com/courses/javascript-module-fundamentals)。

## JavaScript 构建过程

尽管客户端模块加载器支持模块的使用，但是如果有很多模块，我们将再次遇到浏览器同时执行的 get 请求数量有限的问题。

作为构建步骤，没有理由不预先做好模块的加载器“工作”,并因此生成一个包。做这件事的工具的一个例子是 [browserify](http://browserify.org/) 。

Browserify 的名字来源于这样一个想法，即允许在浏览器中使用模块，就像在 Node.js 中使用模块一样。它是 Node.js 模块的“browserification”(使用 commonjs 格式)。

要用 browserify 创建一个包，我们只需要指定什么是主模块。Browserify 将计算出该模块依赖于哪些其他模块，以及这些模块依赖于哪些其他模块等等。

在我们的示例中，我们可以通过这样做来创建一个包:

```
$ browserify game.js --outfile bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们只需要将我们的包包含在我们的网页中，我们就可以开始了。

## 蒸腾

众所周知，JavaScript 对类型的要求很宽松。在 JavaScript 中，你不需要指定变量的类型，函数的返回类型或者参数的类型。

这使得创建工具来帮助开发人员变得困难。一些 IDE 会提供一些智能感知信息(例如 [Visual Studio](https://madskristensen.net/post/improved-javascript-intellisense-in-visual-studio) )，但是体验从来都不是完美的。

TypeScript 是一种语言，它是 JavaScript 的超集，允许添加类型信息。

要使用 TypeScript，您需要将其编译成 JavaScript。翻译就是将一种语言编译成另一种语言的过程。

下面是使用 TypeScript 的函数定义的样子:

```
function getPlayer(id: number) : IPlayer {
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们说`getPlayer`函数期望一个名为`id`的参数，它是一个数字，并返回一个`IPlayer`。在 TypeScript 中，您可以定义接口，例如`IPlayer`可以是:

```
interface IPlayer {
  id: number;
  name: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

当您编译这个 TypeScript 代码时，接口对输出没有影响，但是在开发 type 期间，当您拥有一个`IPlayer`实例时，您将获得 intellisense。同样，如果你将一个字符串作为参数传递给`getPlayer`(例如`getPlayer("abc")`)，你也会得到一个错误，你也会得到关于函数参数及其类型的智能感知，在这种情况下，你会得到类型为`number`的`id`的智能感知。

TypeScript 绝不是第一种将文件转换成 JavaScript 的语言。第一个真正流行了一段时间的是 [CoffeeScript](http://coffeescript.org/) ，然而(至少在我看来)它似乎正在消失。

因为它提供了更好的开发体验，TypeScript 可能负责让更复杂的项目用 JavaScript 来完成。此外，因为现在为 JavaScript 构建步骤非常普遍，所以为 transpilation 增加一个步骤只会增加很少的摩擦。

虽然 TypeScript 可能是最流行的转换到 JavaScript 的语言，但应该提到的是，只写 ES6 代码，JavaScript 的新版本，也很受欢迎。由于目前的浏览器并不支持 ES6 的所有特性，所以 ES6 代码也被移植到当前版本的 JavaScript 中。实现这一点的工具是[巴贝尔](https://babeljs.io/)。

## 打造类固醇工具

想象一下用 JavaScript 加载图片或 CSS，而不是用 HTML。这就是像 [Webpack](https://webpack.github.io/) 这样的构建工具所支持的。

如果这是你第一次听说这件事，你可能会想这怎么会是个好主意。事实证明，它支持解决 web 开发中一些常见问题的场景。与我们现在在 JavaScript 中拥有模块的方式相同，我们可以将相同的解决方案应用于 CSS，如果我们通过 JavaScript 导入 CSS，该 CSS 可能会被[限定范围，这样它就不会与页面](https://vimeo.com/116209150)中的任何其他 CSS 进行交互。

CSS 中的图像可以自动转换为 base64 格式，如果它们小于某个阈值大小，就可以嵌入 CSS 本身。

这些只是 Webpack 所能实现的一些例子。如果你花些时间熟悉它，你会发现 Angular 的新版本非常依赖这种功能。

## 结论

在这篇文章中，我试图描述我是如何看待 JavaScript 演变成今天这个样子的。一开始，JavaScript 是一种简单的语言，现在仍然是，但它周围没有这个活跃的生态系统。这个生态系统的大部分都是通过解决 JavaScript 的使用方式所导致的问题来实现的。随着在 Node.js 中完成的大量可共享工作，以及在浏览器(Browserify)中以类似方式使用这些工作的方式，JavaScript 生态系统得到了极大的发展。它继续随着 Webpack 等工具的发展而发展，这些工具促进了以可管理的方式实现更多复杂性的场景和实践。