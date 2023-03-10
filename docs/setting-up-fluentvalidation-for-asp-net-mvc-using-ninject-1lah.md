# 使用 Ninject 为 ASP.NET MVC 设置 FluentValidation

> 原文：<https://dev.to/robinvanderknaap/setting-up-fluentvalidation-for-asp-net-mvc-using-ninject-1lah>

[*fluent validation*](http://fluentvalidation.codeplex.com/)*是一个强大的库，可以流畅地验证你的模型对象。设置 FluentValidation 很容易，将掘金包导入到您的项目中，您就可以开始了。可以使用 IoC 容器来设置 FluentValidation，这在您的验证器包含一些您想要使用依赖注入来移除的依赖时非常有用。在本文中，我将向您展示如何使用 Ninject 来设置 FluentValidation。*

### 有一个 NuGet 包可以解决这个问题！

我假设您已经为您的项目设置了 FluentValidation 和 Ninject。你只需要一个小软件包，你也可以从 NuGet 获得。这个包包含一个定制的 validatorfactory，它让 Ninject 负责创建验证器的实例。

首先，您必须创建 ninject 工厂，并将 FluentValidation 配置为使用这个工厂而不是默认工厂。将以下代码行添加到 Global.asax 中的 Application_Start 方法中，或者如果您在 App_Start 中的 fluentvalidation 启动类的 Start 方法中使用 webactivator: