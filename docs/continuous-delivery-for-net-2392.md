# 连续交货。网络(第一部分)

> 原文：<https://dev.to/mozzydev/continuous-delivery-for-net-2392>

作为一名开发人员，一个非常重要的因素是将您的代码部署到适当的环境中，而不出现任何问题。为了做到这一点，我们应该将尽可能多的任务自动化，以减少人为失误。当然，一定程度的人与人之间的互动应该会发生，但是总的来说，一旦我们建立了部署过程，我们不应该需要做太多。

## 简单指南

这是非常基本的，每次我们做项目时都应该这样。

1.  创建项目+回购
2.  写一些代码
3.  推送一些代码到 repo
4.  抓取代码并推送到服务器

现在...第 1，2 和 3 部分将在很大程度上是相同的，但第 4 部分可以通过多种不同的方式完成，并且需要各种不同的设置/更新来获得一个工作网站。让我们不要过多地讨论不同选项的细节，而是看一下实现这一点的特定方法。

## 团队城市和章鱼部署

为了便于我们部署，我们将使用 TeamCity 和 Octopus Deploy。这样做需要一些配置，但是这应该比传统的 Web Deploy 方法简单。

值得注意的是，部署的目标之一是允许流程中有不同的角色。因此，开发人员可以推送代码，然后系统管理员或技术主管可以管理部署。因此，从 Visual Studio 发布是完全不可能的。这有几个好处，因为相关人员负责他们的职权范围。例如，一个开发人员没有能力将变更推向生产，因此涉及到一定程度的保护/签署。系统管理员可以创建一个新的服务器，如果他们愿意的话，可以在更精细的层次上查看推送的内容。重要的服务器细节没有公开，这可能是 Web Deploy 的情况。

下面是设置所需步骤的基本概述...

1.  在生成服务器上安装 TeamCity(仅一次)
2.  安装 [Octopus TeamCity 插件](http://docs.octopusdeploy.com/display/OD/TeamCity)(仅一次)
3.  在构建服务器上安装 [Octopus 服务器](http://docs.octopusdeploy.com/display/OD/Installing+Octopus)(仅一次)
4.  为项目创建服务器(每个项目仅一次)
5.  在需要的服务器上安装[章鱼触手](http://docs.octopusdeploy.com/display/OD/Installing+Tentacles)(每台服务器只安装一次)
6.  通过 NuGet 在项目中安装 [OctoPack](http://docs.octopusdeploy.com/display/OD/Using+OctoPack) (每个项目只安装一次)

如图所示，这些任务中有许多您不需要重新执行，一旦完成，您就可以在不需要手动干预的情况下进行部署。

**TeamCity**

用于构建解决方案、执行测试和创建用于部署的工件。这将为开发分支建立持续的集成，这样我们就不必每次都触发发布。我们可以使用一个模板来跨项目共享它。

1.  签出存储库并构建解决方案(运行 OctoPack)
2.  执行测试并检查代码覆盖率
3.  将包发布到 Octopus NuGet 服务器
4.  创建八达通释放(触发八达通部署)

**章鱼展开**

用于将工件部署到给定的环境，以及在 IIS 中建立网站和为不同的 SQL 连接更新变量。这一切都发生在我们通过安全连接部署到的服务器上。

1.  抓取 NuGet 包
2.  在 IIS 中创建网站
3.  将文件部署到网站文件夹
4.  更新变量/应用转换
5.  清理/应用保留策略

当一个版本被创建时，Octopus Deploy 将无限期地保存它，除非你告诉它这样做。这意味着您可以快速回滚到以前的版本，但也意味着您可能会在服务器上留下一些您不想要的文件。因此，在 Octopus Portal 中，您可以修改想要保留的发布次数和天数。更多信息可在[文档](http://docs.octopusdeploy.com/display/OD/Retention+policies)中找到。

为什么两者都用？

的确，您可以使用 TeamCity 来部署到每个环境中，并为您执行配置。但是，这依赖于您希望部署到的服务器上安装的 Web Deploy。这不是 Octopus Deploy 的要求，Octopus Deploy 使用触角的思想来打开构建服务器和 web 服务器之间的通信。这有几个[安全增强](http://docs.octopusdeploy.com/pages/viewpage.action?pageId=360622)，通常更容易配置。您还可以让 Octopus Deploy 在 IIS 中建立一个站点，或者为您执行 Powershell 任务，这是 TeamCity 和其他构建平台所不具备的。

总而言之，它是关于使用为手边的任务而构建的东西。TeamCity 可用于构建代码、运行测试和创建单一发布包。然后可以使用 Octopus Deploy 将这个 NuGet 包部署到您想要的任何地方，并确保配置对于环境是正确的，甚至在这个过程中清理文件。

如果你想了解更多，有一篇[博客文章](http://octopusdeploy.com/blog/octopus-vs-build-server)详细介绍了这种方法，解释了为什么创建 Octopus Deploy。

## 示例设置

*   1 台安装了 TeamCity 和 Octopus 服务器的构建服务器
*   1 台网络/数据库服务器，用于内部开发/QA(共享网络和数据库)
*   1 台用于 UAT 的 web/db 服务器(可以是单独的 web 和 db)
*   1 台用于生产的 web/数据库服务器(可以是单独的 web 和数据库)

在你的项目中，你需要下面的**转换**...

*   网络。开发配置
*   网络。QA.config
*   网络。UAT.config
*   网络。生产配置

当 Octopus Deploy 上传到网络服务器时，这些将自动运行。然而，有几件事情需要注意。确保转换与 Octopus Deploy 中的环境名称相匹配，并确保文件包含在 NuGet 包中。有关这方面的更多说明，请查看[文档](http://docs.octopusdeploy.com/display/OD/Configuration+files)。

Octopus Deploy 也有这个概念，即可以在 Octopus 门户(管理区)中设置变量。这意味着您可以定义您的变量，并指定这些变量将应用于哪个环境。只要您为项目打开了这些选项，它们就会覆盖您的配置文件(不仅仅是 web.config)中的名称/值对。还有一个额外的好处是能够定义变量集并在项目中继承它们。一个这样的例子是一组日志选项，您只为 UAT 和生产启用，但是您倾向于在每个项目上都这样做。有了变量集，您可以确保它们包含在很少的配置中。更多示例见[文档](http://docs.octopusdeploy.com/display/OD/Variables)。