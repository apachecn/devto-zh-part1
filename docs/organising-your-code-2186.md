# 组织您的代码

> 原文：<https://dev.to/chrisrhymes/organising-your-code-2186>

开始编写代码而不考虑组织是很容易的，但是花一些时间来计划，它会在以后回报你。有时候，一个新项目是如此令人兴奋，以至于你只想开始编码并让事情运转起来，以为你会在以后整理出这个结构。毕竟，只要有用，谁在乎呢，对吧？

编码是一个创造性的过程，一旦你进入状态，你就不想打断它。几个文件会突然变成几个文件，接着是几个 JavaScript 库，然后是一些 css 文件、图片，突然间你会发现自己陷入一片混乱。

花些时间整理你的文件结构可以节省你很多时间，也有助于你将来保持理智。

## 文件结构

在开始之前，您可以采取的一个非常简单的步骤是在项目文件夹中创建一系列空文件夹。你可以从一个 JavaScript 文件夹、一个 CSS 文件夹和一个图片文件夹开始。简单却有效。

这个简单的步骤让你思考你的文件将存放在哪里，以及你将如何对它们进行分类。例如，如果你正在写博客，你可能想创建一个文章文件夹，把你所有的文章文件放在那里。它会让你思考你的文件在哪里，并最终使你的 url 路径更加合理。

您可以考虑的下一步是您想要包含的库。过去，下载一个 JavaScript 文件并将其保存在 JavaScript 目录中是很简单的，但是现代的库通常可以拆分成许多不同的文件。

您不再需要手动复制和粘贴这些文件。使用包管理器。

## 使用包管理器

包管理器或依赖管理器负责导入您需要的所有文件。有一些工具，如 [npm](https://www.npmjs.com/) 或 [bower](https://bower.io/) 可以用来将包拉入到您的项目中供您使用。

在你使用软件包管理器之前，还有一些工作要做，因为你可能必须首先在你的系统上安装它们，但是一旦你安装了它们，你就可以在你所有的项目中重用它们。

首先初始化项目中的包管理器，然后开始添加依赖项。软件包管理器将会为您把软件包安装到一个目录中。

另一个很大的好处是软件包在版本控制之下。软件包更新了错误修复和增强功能，因此要充分利用这些功能，您只需运行 update 命令，软件包就会自动更新。

## 使用一种方法论

你工具箱中的另一个工具是方法论。编写网页的传统方式是在 index.html 文件中编写 HTML，加上一些内嵌样式，然后在页面中散布一些 JavaScript。然后，您可以开始在同一个文件中包含一种编程语言，比如 PHP。这很快变成了不可维护和不可重用代码的噩梦。

这个世界已经从这种状态中走了出来，现在人们倾向于更加逻辑地思考他们的编码方式。有许多不同的方法适合不同的环境，所以最好做一些研究，看看什么适合你的项目。组织代码的一种常见方法是 MVC(模型、视图、控制器)，其中代码的每个部分都执行特定的用途。我现在不打算详细讨论这个问题，但是网上已经有了大量的信息。

## 使用框架

另一种方法是从框架开始，比如 PHP 的 [Laravel](http://laravel.com/) ，或者前端的 [Foundation](http://foundation.zurb.com/) 。这些框架是一个很好的起点，意味着您有一个起点和一个现有的文件夹结构可以遵循。

框架是有用的，因为它们提供了许多常用的方法，但首先要研究它们是如何工作的，因为要获得最大的好处，你需要按照它们想要的方式工作，否则你会发现自己试图撤销它们已经完成的工作，以使它按照你的方式工作。那么使用框架就没有意义了。

这个博客是使用 Jekyll 创建的，托管在 GitHub 页面上。这是一个很好的系统，因为它提供了许多基本的功能，如流动模板和对页面和帖子的支持，但你仍然控制着前端的 HTML，JavaScript 和 CSS。

我再说一遍，利用框架的优势。

## 现在都整理好了？

希望你在开始下一个项目之前花点时间计划一下，即使是在一张备用纸上画个简单的草图。你以后会感谢自己的，相信我！