# 用新的 API 构建定制工具，或者说，我如何学会不再担心并爱上 Sitecore

> 原文：<https://dev.to/wintermute21/building-custom-tooling-with-a-new-api-or-how-i-learned-to-stop-worrying-and-love-sitecore>

因此，我在一家大型人事公司工作，我们有几个基于传统 ASP.NET 网络表单的网站，有大量的用户数据要迁移到我们的常规 CMS 系统 Sitecore 上。

Sitecore 是一个基于 ASP.NET 的 CMS，同时存在于 Web 表单和 MVC 风格中。我们主要使用 Web 表单版本。

我们的项目是对我们已经拥有的一个遗产网站的重新设计。我们有一些博客文章和其他数据，我们必须进行重复数据删除和清理。我的代码可能只有 500 行，但是这个任务对于团队的其他成员来说是个棘手的问题。我向同事们推荐了这个工具，他们让我使用它。

我们有一堆数据和重复数据需要清理并导入到 Sitecore，所以我研究了 Sitecore API，将我们所有的数据转换成 Sitecore 的传统数据结构，称为 Items。和项目存在于不同种类的对象中，这取决于您从哪个模板派生而来，文件夹、页面、布局、子布局等等。

所以我写了一个工具，可以从我们已经有的数据中剔除重复的数据，将它们全部转移到 Sitecore 项目中，在那里可以很容易地使用 Sitecore 进行操作和存储。来自 SQL Server 的. NET API。

这个工具运行起来需要一段时间，但是它第一次就成功了，并且节省了其他三个人几周的时间。而且我只花了一周左右的时间就写好了工具本身。