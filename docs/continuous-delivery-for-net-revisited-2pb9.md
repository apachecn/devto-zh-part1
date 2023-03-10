# 连续交货。网络(重访)

> 原文：<https://dev.to/mozzydev/continuous-delivery-for-net-revisited-2pb9>

去年，我写了关于部署和持续交付的想法。网。在那篇文章中，我谈到了如何设置和配置 TeamCity 和 Octopus Deploy 作为部署工具，并围绕这个过程添加了一些注释。

事情在变化，不断的改进，所以这是对那篇文章的回顾，也是对过程如何修改的更新。好消息是，第一篇文章中选择的工具已经成为部署过程中的主要工具，我们在这里讨论的是一种改进。重申目的...

> TeamCity 是构建工具，Octopus Deploy 是部署工具。

## TeamCity

如前所述，我们使用 TeamCity 来构建解决方案，执行测试，并为部署创建工件。我们还使用一个模板，这样就可以在项目间共享。

1.  配置版本( [GitVersion](https://github.com/GitTools/GitVersion) )
2.  获取包，构建解决方案并运行 OctoPack
3.  前端任务(例如 npm 安装/咕哝/吞咽)
4.  执行测试并检查代码覆盖率
5.  将包发布到 Octopus 部署 NuGet 服务器

根据需要，您可能希望将它分成 2 个或 3 个配置。这样做的原因可能是，如果您想要单独运行您的单元测试(它们可能需要很长时间)，或者如果您想要将您的工件发布为依赖项(即，手动任务来推送在您的其他配置中创建的工件)。

### GitVersion

我们在 TeamCity 中将它作为一个元运行器，关于如何使用这个工具，还有其他选项，例如命令行或 MSBuild 任务。这是基于我们的 git 历史自动为我们创建 SemVer 或 NuGet 版本。以前，我们手动进行了大量的版本控制，然后我们用 powershell 脚本来实现类似的结果，直到我们偶然发现这个为我们完成了所有工作。它遵循 Gitflow 和 Github 流约定，并可以通过一些配置应用于其他分支策略。

## 章鱼调遣

用于将工件(NuGet 包)部署到给定的环境中，以及在 IIS 中设置网站和为不同的 SQL 连接更新变量。我们这里的过程大体上是相同的，但是 Octopus Deploy 从那时起已经有了一点发展，现在已经支持生命周期、自动版本创建和(在下一个版本中)离线部署以及其他一些真正有用的特性。

1.  测试 SQL 连接
2.  抓取 NuGet 包
3.  在 IIS 中创建/更新网站
4.  将文件部署到网站文件夹
5.  更新变量/应用转换
6.  URL 测试(200 确定)
7.  通过松弛时间通知部署状态
8.  清理/应用保留策略

步骤 2、3、4 和 5 实际上可以通过 Octopus Deploy 中的一个步骤来完成(部署一个 NuGet 包)，但是为了更好的可读性，我在这里把它分开了。我们还围绕我们的部署添加了一些基本测试...

*   测试 SQL 连接:如果我们不能使用提供的连接字符串访问数据库，我们就不部署
*   测试 URL:我们确保一旦部署完毕，我们将获得 200 OK 状态
*   通知状态:我们使用 Slack 发送部署状态(如果您喜欢传统方法，也可以发送电子邮件)

根据项目或使用情况，您可能需要执行其他步骤，例如备份数据库或网站文件夹，授予特定用户权限，或者通过 Chocolatey 安装部署所需的包。step 模板库中还有很多其他选项:[https://library.octopusdeploy.com/](https://library.octopusdeploy.com/)

### 生命周期

这是在 2.6 中引入的，允许在不同环境之间构建部署过程。因此，您可以将其设置为不再允许发布包未经任何测试就直接部署到生产环境中。这迫使您通过适当的部署流程发布产品，并在升级前获得签署。

因此，一个生命周期的例子可以这样建立...

**内部测试**

*   开发(自动部署)
*   质量保证

**客户端测试**(任意 1)

*   UAT
*   预生产

**上线**(任意 1)

*   生产
*   灾难恢复(备份服务器)

在此范围内，您可以指定是需要部署所有的环境，还是至少需要部署生命周期阶段中的一个环境，如上面括号中所示。您还可以为每个阶段设置不同的保留策略，因此您可能希望将所有版本保留在“上线”阶段，但可能是最新的 5 个版本处于“内部测试”和“客户端测试”阶段。

### 3.0

Octopus Deploy 的新版本已经发布。随之而来的是一系列的变化和新功能...

*   允许离线部署、Azure 网站、SSH 和 Linux 支持的部署目标
*   使用 SQL Server 而不是 RavenDB 重建
*   改进的性能
*   一种叫做 [delta compression](http://octopusdeploy.com/blog/the-octopus-deploy-3.0-time-saver-delta-compression) 的东西，它只传输你的包中已经改变的东西，应该会使部署更快。
*   迁移工具，这样您就可以将配置导出到 JSON 中，并导入到 Octopus Deploy 的其他实例中
*   触手架构的变化，这意味着触手的部署方面不再与 Octopus 版本紧密耦合。进入 Calamari，这是一个命令行工具，在部署过程中由触手调用，用于执行部署任务。也是[开源](https://github.com/OctopusDeploy/Calamari)，可以分叉，自己做。