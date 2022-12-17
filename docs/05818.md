# 用 VSTS 创建你自己的 nuget 包

> 原文：<https://dev.to/funkysi1701/creating-your-own-nuget-packages-with-vsts-297p>

有一段时间，我发现自己为不同的 web 项目编写相同的代码。这让我很恼火，因为这违背了 DRY 原则(不要重复自己)。

[![Nuget](img/9717940de0301e7b61d60f127d4aa3fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oRQ4PSay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.funkysi1701.com/wp-content/uploads/2017/07/nuget.png%3Fresize%3D300%252C91%26ssl%3D1) 一个可能的解决方案是编写自己的 nuget 包。然后，您可以将这段代码添加到您正在处理的任何项目中。

nuget.org 是一个公共的 nuget feed，任何开发者都可以在这里下载 nuget 包。您可以在这里发布您的 nuget 包，但是您可能希望更好地限制访问，以便创建一个私有的 nuget 提要。

让我们看看如何在 Visual Studio 中创建 nuget 提要。你需要做的第一件事是安装 Visual Studio Team Services 的[包管理](https://marketplace.visualstudio.com/items?itemName=ms.feed)扩展(少于 5 个用户是免费的)，这将在 build 菜单下添加一个 packages 部分。

在开始使用这项新功能之前，您需要在用户中心添加软件包管理许可证。

完成后，您就可以创建提要了。您需要为您的订阅源命名，决定是只有当前项目的成员，还是您帐户中的每个人都有权阅读和投稿。

现在您有了一个提要，您可以使用 nuget package 命令创建一个 nupkg 文件，然后使用 nuget push 命令将它添加到您的提要中。更好的方法是让 Visual Studio Team Services 来完成所有困难的工作。

在 Visual Studio Team Service 中创建一个新项目来存放您的 nuget 包。在“生成”部分中，添加一个空的生成定义。选择一个构建代理，我正在使用托管的 VS2017。然后添加以下步骤 nuget restore、Visual Studio Build、nuget pack 和 nuget push。

*   nu get restore——只有当您的代码依赖于其他包时，才需要这一步。如果它依赖于您的 feed 中的其他包，您必须在 feed 和 authentication 部分指定您的 feed。
*   Visual Studio Build–像在 Visual Studio 中一样构建代码。我对这一步所做的唯一配置是在配置中指定 Release。
*   nu get pack——这将从您构建的项目创建 nupkg 文件。在 configuration to package 中，指定与您在上一步中指定的相同的内容(在我的示例中为 Release)
*   nu get push——发布到你的 feed，所以你当然需要指定你的 feed。

最后要配置的是启用触发器中的持续集成选项。这意味着无论何时你推送代码，所有这些步骤都会运行，你就有了一个新版本的 nuget 包。

在 Visual Studio 中，您需要创建一个*。nuspec 文件，它包含了关于 nuget 包的所有元数据。让我们看一个例子。

。要点表{ margin-bottom:0；}

最后要提到的是版本号。您可以在*中更改版本号。nuspec 文件。这将创建稳定的软件包，如 1.0、1.1、1.2 等

但是，您可以在 nuget 包构建步骤中使用自动版本号设置。然而，我发现这只会创建预发布包，我还没有找到一种方法来将一个包从预发布升级到稳定版。

这是在多个项目中重用代码的一种非常好的方式。我只看了几天，我已经提取了电子邮件代码，创建 excel 下载和数据库相关的方法。我怀疑这样做还有一个好处，那就是迫使我创建具有更少依赖性的代码，这样就可以将更多的代码转换成 nuget 包。

帖子[与 VSTS](https://www.funkysi1701.com/2017/07/31/creating-nuget-packages/) 一起创建你自己的 nuget 包最先出现在 [Funky Si 的技术讲座](https://www.funkysi1701.com)上。