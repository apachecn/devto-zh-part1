# AutoPoco，基于您的领域对象创建有意义的测试数据。(.网)

> 原文：<https://dev.to/robinvanderknaap/autopoco-create-meaningful-test-data-based-on-your-domain-objects-net-3mgj>

至少可以说，为你的应用程序创建测试数据通常是一件麻烦且不太鼓舞人心的事情。最近我发现了一个叫做 [*AutoPoco*](http://autopoco.codeplex.com/) *的好工具，它是由*[*Rob Ashton*](http://codeofrob.com/)*创造的。AutoPoco 使您能够基于您的域对象(Poco)生成有意义的测试数据。AutoPoco 能够为单个单元测试生成数据，或者您可以使用 AutoPoco 生成大量的测试数据来填充您的数据库。*

在本文中，我将对 AutoPoco 做一个简短的介绍，并解释如何定制 AutoPoco 来生成对您的项目有意义的测试数据，而不仅仅是一些随机生成的值。

我还在 github 上创建了一个示例应用程序,其中包含一些基本的用法和一些我编写的自定义数据源。示例应用程序的实时版本位于[这里](http://autopocodatasources.skaele.nl/)

**注意:**本文中的源代码基于驻留在主干中的最新版本的 AutoPoco。主干包含了官方 1.0 版本的一些突破性变化，因此本文中使用的源代码不能在 v1 版本中使用。主干位于[这里](http://autopoco.codeplex.com/SourceControl/list/changesets)。如果你不想自己编译 dll，也可以使用示例应用程序中包含的 autopoco.dll。当 Rob 决定发布 AutoPoco 的下一个版本时，我将更新本文和示例应用程序。

### 它是如何工作的？

基本上，AutoPoco 使用反射检查您的域对象，并为您的域对象的每个属性/字段分配数据源。这种分配可以自动完成(约定优于配置)，或者您可以自己分配数据源。当生成测试数据时，每个数据源都会创建有意义的测试数据来填充分配的属性。这里有一个简单的例子:

首先，创建一个将为其生成测试数据的域对象: