# JavaScript 硒摩卡快速入门指南

> 原文：<https://dev.to/stephencavender/javascript-selenium-mocha-quickstart-guide-5a5k>

## JavaScript、Selenium、Mocha 入门！

在本文中，我们将使用 Mocha 和 Selenium 为 web 应用程序编写测试。这将是一个起步项目，我们可以为各种项目和未来的文章。

### 要求

在我们开始之前，以下是一些要求:

*   [NodeJS](https://nodejs.org) (一个 JavaScript 运行时)
*   [Selenium JavaScript 语言绑定](http://docs.seleniumhq.org/download/)(但是我们将使用 NPM 来获取这些)
*   Mocha JS(Mocha 是一个 JS 测试框架)
*   [ChaiJS](http://chaijs.com/) (Chai 是断言库)
*   您想要测试的任何安装的浏览器[支持的平台](http://docs.seleniumhq.org/about/platforms.jsp)

### 硒制剂

如果你还没有通读我对 Selenium 的快速概述，你现在应该通读了。Selenium 需要配置一些东西才能发挥它的魔力！

### NodeJs 教程

如果你不熟悉 NodeJS，我推荐这篇教程来帮助你入门: [NodeHero](https://blog.risingstack.com/node-hero-tutorial-getting-started-with-node-js/) 。里面有很多有用的信息。这是一个很好的资源，让你在阅读这篇文章时保持开放。

### 创建项目

首先，我们需要一个新项目。使用命令提示符或资源管理器，我们需要为我们的项目创建一个新文件夹。一旦我们有了我们的文件夹，我们可以使用命令提示符开始安装我们需要的包。

### 初始化项目

NodeJS 有自己的包管理器:NPM。它还用于初始化项目。运行以下命令并填写每个部分:`npm init`。这将创建 **package.json** 文件。该文件跟踪相关性和项目信息。

### 安装软件包

NPM 还负责安装包。安装包的命令是`npm install <package>`。需要装硒，摩卡，柴。

*   `npm install selenium-webdriver --save`
*   `npm install mocha --save`
*   `npm install chai --save`

这些将安装在 **node_modules** 文件夹中，并在我们的 **package.json** 文件中被跟踪。

下面是我们的文件夹结构的样子: [![Folder structure](img/43dcb273987063638886e2527c06b8bf.png "Folder structure")](https://res.cloudinary.com/practicaldev/image/fetch/s--cKsLkUtJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cavender.io/asseimg/js-selenium-mocha/structure.png)

我们现在可以编写我们的第一个测试并使用我们安装的包了！此时 package.json 文件应该是这样的:

```
{
"name": "selenium-js-mocha",
"version": "1.0.0",
"description": "A project to go along with blog posts explaining how to get started with functional testing using Selenium, JavaScript and Mocha. ",
"main": "test1.js",
"dependencies": {
"chai": "^3.5.0",
"mocha": "^2.5.3",
"selenium-webdriver": "^2.53.2"
},
"devDependencies": {},
"scripts": {
"test": "mocha test1.js"
},
"author": "Stephen Cavender",
"license": "ISC"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 写测试

我们将针对互联网 <sup id="fnref:theinternet">[1](#fn:theinternet)</sup> 编写我们的测试。

让我们在项目文件夹中创建一个新的 JS 文件:我将创建 test1.js.

```
// Load dependecies
var assert = require('chai').assert,
test = require('selenium-webdriver/testing'),
webdriver = require('selenium-webdriver');

// Our test
test.describe('Test', function () {
test.it('Title should be "The Internet"', function () {
// Set timeout to 10 seconds
this.timeout(10000);

// Get driver
// var driver = new webdriver.Builder().
// withCapabilities(webdriver.Capabilities.firefox()).
// build();
// var driver = new webdriver.Builder().
// withCapabilities(webdriver.Capabilities.edge()).
// build();
// var driver = new webdriver.Builder().
// withCapabilities(webdriver.Capabilities.ie()).
// build();
var driver = new webdriver.Builder().
withCapabilities(webdriver.Capabilities.chrome()).
build();

// Go to URL
driver.get('http://the-internet.herokuapp.com');

// Find title and assert
driver.executeScript('return document.title').then(function(return_value) {
assert.equal(return_value, 'The Internet')
});

// Quit webdriver
driver.quit();
});
}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  加载依赖项
2.  创建测试
3.  设置超时(测试运行太快，并且在不增加超时的情况下失败)
4.  获取网络驱动程序
5.  设置 URL 属性(告诉驱动程序转到该 URL)
6.  坚持司机的头衔
7.  处理掉司机

### 运行测试

现在我们有了一个功能测试，我们可以运行它。保存测试文件，然后让我们打开命令提示符。为了运行测试，我们调用 mocha 命令和我们的测试文件。

`mocha test1.js`

在命令提示符下运行，我们应该会看到我们的测试运行，命令提示符会告诉我们测试的结果。如果用测试脚本设置了 **package.json** ，我们也可以通过调用 npm 测试脚本来运行我们的测试。

`npm test`

这就是命令提示符如何显示我们的测试运行: [![Command prompt test run](img/0b5f36873fd8e2f17b0c9a7e52fbd23d.png "Command prompt test run")](https://res.cloudinary.com/practicaldev/image/fetch/s--SLB_R85b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cavender.io/asseimg/js-selenium-mocha/npmTestCmd.png)

bash 是这样显示我们的测试运行的: [![Bash test run](img/b17632a56aa6be3749fda87b837a9b00.png "Bash test run")](https://res.cloudinary.com/practicaldev/image/fetch/s--nVxE4FQB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cavender.io/asseimg/js-selenium-mocha/npmTestBash.png)

这是硒如何工作的一个基本而脆弱的例子。我们将在以后的文章中介绍一个更好的测试方法来避免这样的事情！这绝不是最佳实践的例子。这是给你一个硒元素的例子。请继续关注更多关于如何使用 Selenium 的帖子、自动化测试的最佳实践和视频教程！

感谢阅读！如果你觉得这篇文章有帮助，一定要分享，不要犹豫，和我聊聊吧！

归功于戴夫·海夫纳。↩

最初发布于 [cavender.dev](https://cavender.dev/selenium/js-selenium-mocha-quickstart/)