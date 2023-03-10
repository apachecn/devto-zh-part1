# 使用 npm、webpack 和 TypeScript 在 Visual Studio 2017 中设置 ASP.NET 核心:第一部分

> 原文：<https://dev.to/t4rzsan/setting-up-aspnet-core-in-visual-studio-2017-with-npm-webpack-and-typescript-part-i>

这篇文章最初出现在我的个人博客上。

### [Github](https://github.com/t4rzsan/aspnet-core-with-webpack/tree/master)上的示例代码。

在过去的几年中，web 开发的发展速度非常快。网站页面刷新的日子已经一去不复返了。年轻人和潮人想要 ajax、动画、spinners 等等。JavaScript 世界已经出现了很多很酷的库，现在最新、最酷、最热的库的变化速度比我换内裤的速度还快(或者我只是变老了，或者我需要更频繁地换内裤)。

最近，微软发布了 Visual Studio 2017 (VS)的最终版本，我想现在是时候了解一下 ASP.NET 核心和所有很酷的 JavaScript 东西了。

在脑海中的情况是一个小的内部网网站，将允许用户做一些快速精算。

**入门**
让我们从创建一个 Visual Studio ASP.NET 核心项目开始，启动 Visual Studio 2017 并从“ASP。NET 核心 Web 应用程序(。我们将选择这个模板来使代码与非核心程序集兼容(精算程序集是为。NET 4.5.2)。

在对话框的顶部，选择。您希望与之兼容的. NET framework。

[![](img/c6cd0e83cc426e7bc91af34609f5b229.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v4tax85---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://leruplund.dk/wp-content/uploads/2017/04/Udklip-1024x710.png)

在下一个对话框中，选择“Web Application”，Visual Studio 将为您的项目设置一个基本结构。其中，Visual Studio 创建一个名为“wwwroot”的文件夹。这是将要发布到实际 Web 服务器的文件应该放置的位置。不要在该文件夹中放置任何其他东西，如您的代码。

**设置 npm**
为了将所有的 JavaScript 优点融入到我们的项目中，我们需要安装节点包管理器(npm)。顾名思义，npm 是 JavaScript 的包管理器。想想 Javascript 的 Nuget。但是 npm 能做的不止这些。它还可以在 webpack 的帮助下构建 TypeScript，这是我们稍后要做的。

您可以通过 Visual Studio 安装程序安装 npm (Node.js ),但是您应该始终确保您运行的是最新版本。您也可以从 [node.js 网站](https://nodejs.org/en/download/)下载并安装。

现在我们需要初始化 VS 项目目录的节点。打开命令提示符，将 cd 放入 VS 项目文件夹。csproj 文件所在)。

运行命令:

T2`npm init -y`

这将使用默认设置为您的项目初始化 npm。初始化创建了一个名为 package.json 的文件。

您可能希望打开 package.json 文件并将 name 属性更改为全部小写，因为不支持大写字母(我不知道 npm init 为什么不将其更改为小写)。

稍后运行以下命令来更新 npm。

T2`npm install npm@latest`

**安装 webpack**
接下来是 [webpack](https://webpack.js.org/) 。Webpack 似乎是 JavaScript 的瑞士军刀，看起来人们正在从 Grunt 和 Gulp 之类的东西转向 webpack。此外，以我个人的浅见，webpack 文档远比你将看到的许多其他“热门和酷”的开源 JavaScript 库要好。

我们将使用 webpack 编译 TypeScript 和捆绑脚本文件。

通过运行以下命令，使用 npm 安装 webpack

T2`npm install webpack --save-dev`

这将把 webpack 保存为 package.json 中的开发(而不是生产)依赖项。npm install 命令还会在具有依赖项的项目文件夹中创建一个名为“node_modules”的文件夹。不要在你的项目中包含这个文件夹。

**下一篇**
在本系列的[下一篇](https://dev.to/t4rzsan/setting-up-aspnet-core-in-visual-studio-2017-with-npm-webpack-and-typescript-part-ii)中，我们将开始运行打字稿。

Â