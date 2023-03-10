# 更好地处理 npm 模块的 3 个技巧

> 原文：<https://dev.to/voxpelli/3-tricks-to-better-handle-npm-modules-20g9>

使用 npm 模块进行开发不仅仅是安装模块然后更新它们。在团队环境中，您可能甚至不知道什么时候应该安装一个新模块，或者什么时候它的版本需求已经改变。当安装的模块与应用程序的预期不符时，这可能会导致许多奇怪的意外行为——这很烦人，而且是浪费时间。

在这里，我给你三个窍门来避免这种情况。这些年来我已经开始使用的技巧，我们现在正在我的最新项目中使用，为 [Sydsvenskan](http://www.sydsvenskan.se/) 和 [HD](http://www.hd.se/) 开发新网站。

## 1。根据 package.json 验证已安装的版本

当快速开发一个新的网站，建立基本功能等，新的模块得到了相当多的增加。通常在重定基础后，人们会意识到自己缺少了一个模块，因为自己的 [nodemon](http://nodemon.io/) 进程突然崩溃，出现了一些意想不到的奇怪错误。

我创建了[installed-检查](https://github.com/voxpelli/node-installed-check)来解决这个问题。我可以运行一个脚本来检查我安装的模块是否仍然满足 package.json 提出的要求，或者是时候安装更多的模块了。所有检查都在本地进行，没有任何缓慢的网络查找等。

如果有任何模块丢失或者超出了版本要求，它将会出错退出。

然后，我将那个脚本挂接到我的`npm test`脚本和[哈士奇](https://github.com/typicode/husky)(在`postapplypatch`和`postmerge`)中，这样每当我下载新代码或运行我的测试时，它都会验证我的安装是最新的。

有了这一点，团队中的每个人都可以不用担心他们是否在本地丢失了一个模块，我们都可以停止浪费时间调试由于 package.json 需求的变化而导致的问题。快乐的开发者！

## 2。验证 package.json 与实际的模块使用同步

虽然测试可能在本地通过得很好，但是如果一个人没有提交所有的依赖需求，那么他们很难在其他地方通过。

同样，重构的代码可能工作得很好，但是人们可能没有意识到被移除的`require()`是给定模块的最后一个。

因此我总是在我的`npm test`中运行[依赖检查](https://github.com/maxogden/dependency-check)(现在由我共同维护)。确保未提交的依赖关系被及早发现，并且没有多余的模块被保留下来，在它们不再被使用后拖累项目。

我还通过使用 [husky](https://github.com/typicode/husky) 设置一个`prepush` git 挂钩，让`npm test`在代码被远程推送之前运行。这样，无论是我还是团队中的任何人都不会意外地将代码推入任何这样的错误中。(我发现`prepush`在这方面比`precommit`做得更好——更务实，结果是开发人员更开心)

## 3。验证已安装模块的发动机要求

如何表达你的库支持什么版本的 node.js？在 package.json 中有一个[引擎字段](https://docs.npmjs.com/files/package.json#engines)用于这个:

```
"engines": {
  "node": ">=5.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

简单。你知道你支持什么引擎，你礼貌地告诉别人，这样他们也能很容易地找到答案。

但是，当其他人更新他们的需求时，您如何检测，以及您如何避免您得到比您自己有更严格的引擎需求的依赖？肯定有自动验证的方法吧？

检查刚刚发布的[版本`2.0.0`installed-check](https://github.com/voxpelli/node-installed-check):它有一个新的可选标志`--engine-check`，这使得它还可以检查所有已安装依赖项的引擎需求。

如果任何已安装的依赖项的引擎要求与您的不匹配，那么将会返回一个错误，并建议尽可能更严格的引擎要求。

通过在你的`npm test`中运行它，你可以很容易地及早发现引擎需求的变化，或者完全避免这种变化，或者自己发布一个新的更严格的引擎需求的主要版本。(根据[语义版本](http://semver.org/)，改变的引擎需求总是被认为是一个突破性的改变，需要一个新的主要版本)

这种方法的唯一问题是，并非所有模块都在它们的 package.json 中明确定义了它们的引擎要求。默认情况下，installed-check 忽略任何这样的模块，并且不将未定义的引擎要求视为错误。通过设置`--verbose`和`--strict`标志中的一个或两个，可以让它在遇到这样的模块时发出警告或抛出错误。

## 举例:运行所有的诡计

安装模块:

```
npm install --save-dev installed-check
npm install --save-dev dependency-check
npm install --save-dev husky 
```

Enter fullscreen mode Exit fullscreen mode

设置它们运行:

```
{
  "scripts": {
    "test": "installed-check -e && dependency-check . && dependency-check . --extra --no-dev",
    "prepush": "npm test",
    "postapplypatch": "installed-check -e",
    "postmerge": "installed-check -e"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后从更坚实的依赖工作流和更快乐的开发团队中获益！