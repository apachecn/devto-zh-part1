# 数据库部署

> 原文：<https://dev.to/funkysi1701/database-deployment-5331>

我的工作之一是给我们的内部数据库增加额外的功能。做这件事有相当大的风险。我可以对数据库中的一个查询进行更改，以阻止公司的其他人在需要时使用它。这可能是一个代价高昂的错误，几乎与数据库服务器不可访问一样糟糕。

降低这种风险的一个方法是测试、测试和测试数据库。首先，我对数据库进行备份，并对其进行更改。我可以对我的备份数据库做任何我想做的事情，而不用担心它会影响到其他人。一旦我完成了我的更改，并且已经测试出一切都如我所愿，我需要将我的更改应用到生产数据库。

直到最近，我记下了我所做的每一个更改，然后对生产数据库进行了同样的更改。但是，如果我忘记将我的一个更改应用到生产中，我就有一个损坏的数据库，灾难！

但是有一种更好的方法来跟踪数据库的变化。SSDT 或 SQL Server 数据工具。

使用 Visual Studio 创建一个数据库项目，然后可以将它指向您的实时数据库。这将扫描数据库并保存数据库中所有表、视图、存储过程、触发器和任何其他对象的脚本。这并不导入数据，只导入包含数据的结构。所以没有数据保护问题，因为您的数据库是万亿字节大小。

现在真正酷的部分来了，Visual Studio 允许您从这些脚本构建数据库的副本。您所需要做的就是编辑脚本，并像以前一样进行测试。一旦您对您的更改感到满意，并且一切都如您所愿，您可以将您的 visual studio 项目指向您的生产数据库，它将神奇地部署您的所有更改，或者如果您像我一样对破坏东西感到偏执，它将创建一个脚本，您可以在生产数据库上运行之前彻底分析它。

我不知道为什么直到最近我才想到设置它，它将减少我推出我的更改但忘记设置权限的次数，(这些可以通过脚本编写)或者忘记我几周前做的但从未推出的微小更改。

当然，您所做的所有更改都可以保存在源代码控制中，因此很容易看到做了什么更改以及由谁做的更改。

这个过程甚至可以处理复杂的数据库结构，在我的例子中，我需要设置三个相互依赖的数据库项目。可以设置依赖关系，甚至可以用不同的名称构建测试数据库，以便在生产服务器上进行测试，(尽管我不建议这样做！)