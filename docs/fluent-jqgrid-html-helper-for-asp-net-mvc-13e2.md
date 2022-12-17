# ASP.NET MVC 的流畅 jqGrid Html 助手

> 原文：<https://dev.to/robinvanderknaap/fluent-jqgrid-html-helper-for-asp-net-mvc-13e2>

***更新:**jqGrid 助手的新版本发布，在这里*了解更多信息

JqGrid 是一个被广泛采用的 jQuery 网格插件，以其灵活性和大量可用特性而闻名，最后但同样重要的是它是免费的！创建合适的 JavaScript 来创建 jqGrid 并有效地使用可用选项可能有点冗长，通常需要多次访问[文档 wiki](http://www.trirand.com/jqgridwiki/doku.php) 。我决定为 ASP.NET MVC 创建一个 html 助手，它简化了视图中 jqGrid 的实现。

该助手包含一个流畅的界面，提供了一个清晰易读的视图。jqGrid 基类中的大多数属性、事件和方法都在 helper 中实现，包括分页、排序和工具栏搜索。此时，还不支持编辑、树网格和子网格。

### 样本应用

为了展示这个助手，我创建了一个示例应用程序，演示了具有不同配置的各种网格。视图中的源代码显示在每个网格的下面，这应该让您很容易开始。您可以在这里找到示例应用程序。

包括助手在内的整个示例应用程序的源代码可以从 [github](http://github.com/robinvanderknaap/Mvcjqgrid) 获得。对于非 git 用户，在 github 页面上找到“下载源代码”按钮来获取代码。

### 要求

*   jQuery 1.3 或更高版本，当你在 Visual Studio 中创建一个 MVC 项目时默认包含在内，或者你可以在[http://jquery.com](http://jquery.com/)获得最新版本
*   jqGrid 3.6.5 或更高版本，您可以使用[下载构建器](http://www.trirand.com/blog/?page_id=6)创建您自己的定制 jqGrid 包。确保您选择了所有三个基本模块(Grid base、Formatter 和 Custom)，这些模块由 helper 使用，其他模块不受 helper 支持。
*   jQuery 主题，创建你自己的或者从 http://jqueryui.com/themeroller 的图库中挑选一个
*   助手，可从 [github](http://github.com/robinvanderknaap/Mvcjqgrid) 获得(你只需要文件 Grid.cs，位于 HtmlHelpers 文件夹中。)

### 入门

安装 jqGrid 并将助手添加到应用程序之后，您就可以开始在视图中使用助手了。确保在将使用助手的视图页面顶部包含一个导入语句:

或者，如果您计划在项目的多个页面上使用网格，可以在 system.web/pages/namespaces 节点中添加对 web.config 的引用，这将在您的所有视图中启用帮助器:

创建一个基本的网格可能如下所示: