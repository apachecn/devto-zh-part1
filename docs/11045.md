# 快速提示:搜索铁路路线

> 原文：<https://dev.to/sqlsuperhero/quick-tip-grepping-rails-routes>

可以肯定地说，今天大多数 T2 的 Rails 开发人员要么使用 Linux，要么使用 Mac。然而，精通 Rails 开发人员通常不需要*nix 命令行工具的丰富知识。在今天的技巧中，我将展示一个超级简单的用例，使用`grep`命令快速过滤 routes 输出。

您可能知道，`rake routes`(在 rails 5 中也是`rails routes`)打印了应用程序中所有路线的列表——非常简单。然而，随着项目的增长，`routes.rb`文件会很快变得相当大。这可能会使找到你正在寻找的实际路线变得困难，尤其是在添加了几个引擎之后。

解决方法:

```
rake routes | grep KEYWORD
```

这将过滤这些行的输出，以匹配您的关键字。

要返回与控制器相关的所有路由，只需添加控制器的名称:

```
rake routes | grep users
```

要返回带有特定 HTTP 动词的所有路由，只需添加 GET、POST、PATCH:

```
rake routes | grep GET
```

要返回与活动相关的所有路线，请执行以下操作:

```
rake routes | grep update
```

超级简单，但是我发现真的很有用。