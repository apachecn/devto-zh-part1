# 使用浏览器适当调整 AngularJS 应用程序

> 原文：<https://dev.to/aymericbeaumet/properly-shim-angularjs-applications-using-browserify-1h59>

* * *

*为了更好的阅读体验，请阅读最初发布的文章:[https://aymericbeaumet . com/proper ly-shim-angularjs-applications-using-browser ify](https://aymericbeaumet.com/properly-shim-angularjs-applications-using-browserify)T3】*

* * *

编辑(2015 年 3 月 5 日):前端社区在过去的几个月中已经有所发展，并且倾向于对 CommonsJS 风格不那么敌视(例如:Angular 现在可以在 npm 上使用)。这篇文章已经被相应地重写。

AngularJS 是由谷歌开发的前端框架。它允许通过将代码库分成小的组件，以模块化的方式构建高级 web 应用程序。这带来了很多好处，比如关注点的清晰分离和更容易测试。

[Browserify](http://browserify.org/) 是一个工具，它允许在理论上任何 JavaScript 环境中从 [CommonJS](http://wiki.commonjs.org/wiki/CommonJS) 模块中获益。给定一个入口点，它计算一个依赖树，解析它并产生一个输出文件。通过使用`package.json`，Browserify 能够在构建中要求`node_modules`。这允许依赖 [npm](https://www.npmjs.com/) 作为前端依赖项的包管理器，通常会使用 [Bower](https://bower.io/) 或 [Component](http://component.github.io/) 。

当我第一次听说 Browserify 时，我立即想到它带来的模块化将非常适合构建 AngularJS 应用程序。事实也的确如此。然而，到目前为止，它们还不是完美的匹配，有些缺点需要修正。

本文介绍了使用 Browserify 构建 AngularJS 应用程序的解决方案。它涵盖了非 CommonJS 模块作为依赖项的使用。

## 一个示例应用程序

让我们考虑一个样例应用程序，它在本地存储中设置一个条目，并使用它来做出不同的反应，无论用户是第一次加载页面。它将用于帮助识别故障案例，并详细说明如何解决它们。所以创建一个空文件夹，让我们开始吧！

首先，创建一个`app.js`文件，它将成为入口点:

```
var angular = require("angular");
var app = angular.module("app", [require("angular-local-storage")]);

app.run(function (localStorageService) {
  if (!localStorageService.get("lastVisit")) {
    angular.element("body").text("Hello stranger!");
  } else {
    angular.element("body").text("Welcome back!");
  }
  localStorageService.set("lastVisit", Date.now());
}); 
```

Enter fullscreen mode Exit fullscreen mode

很整洁，不是吗？如果你有 Node.js 的背景，我肯定你会喜欢它。

现在让我们生成`package.json` :

```
npm init
npm install --save angular angular-local-storage jquery
npm install -D browserify 
```

Enter fullscreen mode Exit fullscreen mode

然后编译应用程序:

```
./node_modules/.bin/browserify app.js -o bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

最后创建一个基本的`index.html`来加载它:

```
<html>
  <head>
    <script src="bundle.js"></script>
  </head>
  <body ng-app="app"></body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

很简单吧？但是正如你所料，这是行不通的。在网络浏览器中打开`index.html`,查看控制台，发现一个不错的 JavaScript 错误。

## 解释

这里出现了几个问题。

### 未定义入口点的模块

当需要一个模块时，Browserify 在`node_modules`文件夹中查找它的`package.json`并加载它。Browserify 希望它有一个`main`属性，包含应该加载的文件的相对路径。

通常这个属性会丢失，或者指向错误的文件，或者`package.json`本身会丢失。在这种情况下，您可以覆盖文件以加载到自己项目的`package.json` :

```
{  "browser":  {  "angular":  "./node_modules/angular/angular.js",  "angular-local-storage":  "./node_modules/angular-local-storage/dist/angular-local-storage.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 模块无法正确导出其内容

当 Browserify 需要由模块的`package.json`中的`main`属性指示的文件时，该文件通常不会以 CommonJS 样式正确导出其内容。此外，我们希望 AngularJS 模块导出它们的名称，以便我们可以在 AngularJS 模块定义中直接需要它们。

为此，可以使用 [browserify-shim](https://github.com/thlorenz/browserify-shim) 。

首先安装它:

```
npm install -D browserify-shim 
```

Enter fullscreen mode Exit fullscreen mode

然后，更新`package.json` :

```
{  "browserify-shim":  {  "angular":  {  "exports":  "angular"  },  "angular-local-storage":  {  "exports":  "angular.module('LocalStorageModule').name"  }  },  "browserify":  {  "transform":  ["browserify-shim"]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*   `angular`导出全局变量`angular`
*   `angular-local-storage`导出其 AngularJS 模块名`LocalStorageModule`

### 模块获取它们对全局上下文的依赖关系

一些前端模块依赖于这样一个事实，即它们的依赖关系将在全局对象(通常是大多数浏览器中的`window`对象)上公开。这是 CommonJS 架构的反模式，它支持关注点的分离。

例如，AngularJS 期望 jQuery 在`window.jQuery`属性中公开。

一种解决方案是将模块封装到一个函数中，该函数公开一个具有适当属性的全局对象。希望这也可以通过*浏览器填充垫片*轻松完成(安装见上文)。

然后，更新`package.json` :

```
{  "browserify-shim":  {  "angular":  {  "depends":  "jquery:jQuery"  },  "angular-local-storage":  {  "depends":  "angular"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*   `angular`将在`jQuery`酒店的假窗口对象上提供`jquery`模块
*   将以同样的方式向`angular-local-storage`提供`angular`模块

## 总结起来

您应该将以下内容添加到您的`package.json` :

```
{  "browser":  {  "angular":  "./node_modules/angular/angular.js",  "angular-local-storage":  "./node_modules/angular-local-storage/dist/angular-local-storage.js"  },  "browserify-shim":  {  "angular":  {  "depends":  "jquery:jQuery",  "exports":  "angular"  },  "angular-local-storage":  {  "depends":  "angular",  "exports":  "angular.module('LocalStorageModule').name"  }  },  "browserify":  {  "transform":  ["browserify-shim"]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成了这些修改，您应该能够编译并运行示例应用程序:

```
./node_modules/.bin/browserify app.js -o bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

打开`index.html`检查它是否真的工作。

在这篇文章中可以找到一个工作示例[。](https://aymericbeaumet.com/properly-shim-angularjs-applications-using-browserify#attachments)

## 旁注

一些相关的事实可以节省您处理浏览器时的时间:

*   没有发布到 npm 的模块可以通过给出它们的 git 存储库 URL: `npm install git+https://github.com/angular-ui/ui-router.git`来安装。注意，也可以直接传递一个 GitHub 库:`npm install angular-ui/ui-router`。可以通过添加`#tag`来指定版本。更多信息参见[文档](https://docs.npmjs.com/cli/install)
*   不包含`package.json`的模块不能使用 npm 安装(即使直接提供 git 存储库)。为了解决这个问题，必须使用第三方安装程序，如[纳帕](https://github.com/shama/napa)
*   `napa`会直接将依赖项安装在`node_modules`目录下
*   `postinstall`脚本相当于`install`。它在模块安装后执行。然而，我发现它更明确
*   您应该将`napa`放到`postinstall`脚本中
*   在`browser`字段中提供的路径需要相对于根`package.json`路径。每个条目必须由`./`开始
*   在填写浏览器字段时，可以指出文件在另一个存储库中，而不是在`node_modules`中(例如:`bower_components`
*   将 Browserify 选项集中在`package.json`中是一个很好的实践，因为它允许在以不同方式(CLI、API)调用它时保持不变的行为

## 结论

尽管需要编写更多的代码来设置一个项目，但是这个解决方案提供了一种很好的方式来用逻辑块组织它的代码。这完全符合 AngularJS 的思维方式。

此外，在一个地方管理所有的依赖项非常有用。尤其是在开发全栈 JavaScript 应用程序时，因此可能会在前端和后端之间共享依赖关系(例如:Async.js、Bluebird、Moment.js、highlight.js 等)。使用 CommonJS 加载器允许依赖客户端和服务器端完全相同的模块版本。