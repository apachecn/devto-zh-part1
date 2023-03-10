# 如何使用存储过程而不死尝试

> 原文：<https://dev.to/hardkoded/how-to-work-with-stored-procedures-and-not-die-trying-3lal>

## 数据库怎么了？

我不会试图向你推销存储过程是最好的，每个人都需要扔掉他们所有的 ORM 代码，转向数据库世界。我相信每个团队都必须选择最好的工具，不仅帮助他们解决问题，而且使他们更有效率。

无论您是一个有许多存储过程的项目，还是一个数据库极客，或者您只是简单地认为存储过程是您工作的合适工具，如果您想提高团队的生产率，您将面临许多挑战。

我注意到的是，有时，即使在存储过程中编写一个特性是正确的选择，开发人员也不会走那条路，只是因为她觉得在那种技术上没有生产力。这通常与以下一个或多个问题有关:

### 共享数据库

团队倾向于只有一个 SQL Server 和一堆相同数据库的不同版本，例如开发、测试或 CI。

所以，如果一个开发者在开发数据库中做了一个改变，它会立即影响到其他开发者。一些团队试图通过尽可能少地改变数据库来缓解这个问题，并在 Slack 上@channel，发送电子邮件，甚至在办公室里大喊大叫。

### 缺乏源头控制

也许你从来没有想过这一点，但是源代码控制在你的团队的生产力中起着重要的作用。你越容易整合你的代码，跟踪变化或者在任务之间切换，你就越有效率。

很少有团队将他们的数据库置于源代码控制之下，你会怎么做？别担心，我们会到达那里的。

### 没有调试

你是说人们调试程序吗？是...我认为只有两个开发人员这样做，但这是可能的。

## 富有成效

这些是我和我的团队发现的一些技巧和工具，帮助我们在这种情况下保持高效率。

### 数据库是你代码的一部分

即使不使用存储过程，表定义也是代码的一部分。好吧，你可能会说“我是一个代码优先的开发者，我不关心数据库”，这没关系，这篇文章对你没有任何帮助。

但是如果您是“数据库优先”开发人员，表定义应该是代码的一部分。如果在表中添加新字段，则需要创建一个分支，将新字段添加到表定义中，并将所有内容提交到与使用该新字段的代码相同的分支中。

好处是显而易见的，当你的 PR 被接受时，不仅你的代码会被合并，数据库中的新字段也会被合并。

#### 这怎么可能做到？

我在 Visual Studio 中找到了这个问题的解决方案。让我们来看看吧。

##### 创建 DB 项目

让我们以众所周知的 Northwind 数据库为例。只需进入 Visual Studio，点击“新建项目”，寻找“SQL Server 数据库项目”。

[![](img/56dbf32faacb1c72536bad8d17ba6b63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WZ0elBmF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/newproject.png)

#### 导入您当前的数据库

我敢打赌你已经有一个数据库，如果是这样，你将能够使用导入工具导入整个数据库定义。

[![](img/76cb1b8fa778cfc06d7ae7793e9dcd03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--STvnrJ_q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/importoption.png)

然后，您将需要设置数据库连接和导入设置，让我们保持简单。

[![](img/595371c0ae6e074646de8b57c742c095.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qAvyCTN5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/importwizard.png)

瞧啊。现在，您已经准备好将数据库提交到存储库中。

[![](img/bf56bbc02de4b30ddf217389941d745f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YkT0DVGw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/dbproject.png)

### 一个开发者，一个数据库

太好了，我们已经将数据库置于源代码控制之下，现在做什么呢？

下一件事，你必须有一个数据库，每个开发人员，她可以在需要时更新/重新创建它的自由。你可以通过三种方式来实现这一点，也许还有更多方式，但我推荐这三种:

#### 共享服务器

如果您已经有一台供所有开发人员使用的服务器，这可能是最简单的解决方案。您可以使用一种命名约定来标识每个数据库，这种命名约定非常聪明，比如`<MyProject>Dev<DevName>`。

##### 优点

*   没有更多的设置，只需为每个开发人员创建一个数据库，仅此而已。
*   协作。开发人员可以访问彼此的数据库，这使得协作更加容易。

##### 弊

*   如果你现在没有使用共享服务器，你可能需要安装一台服务器，并考虑安全性、VPN 等问题。
*   与本地环境相比，对开发人员来说，这可能是一种较慢的体验。
*   如果一个开发人员正在执行一些繁重的任务，她可能会降低服务器的速度。

#### LocalDB

> LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，旨在用于程序开发。

您可能不知道，但是如果您安装了 Visual Studio，那么您很可能已经安装了 LocalDB。您可以通过打开 **SQL Server 对象资源管理器**来检查这一点。

[![](img/ce805bbca2784c53e0c683626f280fc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bm4YrliG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SQLServerObjectExplorer.png)

如果你在那里没有找到 LocalDB，你可以在这里看到如何安装它。你还可以安装其他版本，比如完整版[微软 SQL Server 开发者版](https://blogs.technet.microsoft.com/dataplatforminsider/2016/03/31/microsoft-sql-server-developer-edition-is-now-free/)。

##### 优点

*   你的 localDB 已经在那里了，你只需要使用它。
*   这是一个本地数据库，你不需要 VPN 或请求访问服务器。
*   本地数据库将加速您的环境，特别是对于远程开发人员。

##### 弊

*   对于您的计算机来说，运行 SQL Server 进程可能非常昂贵，这会降低开发环境的速度。

#### Docker 上的本地服务器

这个解决方案类似于 localDB，但是更适合使用 Mac OS。在 Mac OS 上的虚拟机上运行 Visual Studio 时，在 Docker 容器(直接在 Mac OS 上)上运行 SQL Server 将会提高虚拟机的速度。如果你使用 Mac OS，我强烈推荐这个解决方案。

你可以在 Docker [这里](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker)阅读关于运行 SQL Server 的内容。

### 开发者需要数据

由于每个开发人员都有自己的数据库，因此拥有创建有用且随时可用的数据库的数据成为项目的另一个非常重要的部分。如果您已经有一个数据库，那么创建所有这些数据是非常容易的。

首先，让我们创建一个数据文件夹，在其中保存我们所有的插入脚本。

[![](img/675c98ae7852d956cb8e13ae1cf4a6fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l9tlaXqw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/CreateDataFolder.png)

[SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)有一个非常酷的生成脚本的工具，它叫做...鼓形辊...生成脚本！

[![](img/3d659534c3b28e8522210c834a2da95d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jIK2D-S5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/GenerateScripts.png)

一旦到了那里，我们需要选择我们想要生成的表。

[![](img/f06a50f06c21930114020add365681cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A6zoT18M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/PickTables.png)

然后我们选择位置，我们将需要进入我们在 SQL 项目上创建的数据文件夹。

[![](img/a7996f6fb7db5e885f81d4b1355bff10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s9nDMTop--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SelectLocation.png)

我们需要转到高级选项，并指明我们只需要数据。

[![](img/89ec30be530b60e4ef657286499213a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cbqz5vjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SetupDataOnly.png)

我们完了。

[![](img/da2e85732a4366eed4037dc73d766b50.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iPEtdPNV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/Voila.png)

现在，如果我们返回到 SQL 项目，我们将看到刚刚生成的所有脚本，让我们将这些文件包含在项目中。

[![](img/48e78b371ab2494463d39c6f7932c077.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FncOzGwq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/AddScriptsToProject.png)

[![](img/96852dd934f86ed34228538c1258ca60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ezaahXcP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/AddScriptsToProject2.png)

默认情况下，当我们在项目中包含文件时，构建动作被设置为 **Build** ，但是由于这些文件只是插入脚本，我们需要将它们设置为 **None** 。

[![](img/2fdca5fd339e837aa25a69dd2b28f09a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NtjiV7y6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/DoNotBuildDataScripts.png)

那么，我们如何利用这些脚本呢？我们需要创建一个部署后脚本，并在其中包含所有这些插入。

[![](img/022cb8eff1b685b464965a26ac4e5e4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mWlA5fp9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/PostScriptFile.png)

所以现在我们可以用之前创建的所有插入脚本来完成这个文件。

[![](img/6d86df7d4a30d8b7e67bcabe4bb797b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M29rTbdj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/CompletePostBuildScript.png)

### 数据库同步

完美！我们有一个 SQL 项目，包含我们的表、存储过程，甚至我们的测试数据！现在我们必须使用它。

#### 发布工具

Publish 是您用来从头开始创建数据库的工具。因此，让我们转到发布选项。

[![](img/d44621d1238967ce4268ae524158086f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8XBUbD2V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/PublishOption.png)

一旦到了那里，我们只需要选择我们的连接字符串和目标数据库。

[![](img/dcbea9a96343149b738c10215498aa08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vEZKeDpD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/PublishSelectDatabase.png)

在高级部分，有一些有趣的选项，如“总是重新创建数据库”或“如果可能发生数据丢失，阻止增量部署”。

[![](img/86d4bba2c89371edb6fb7c8e21243add.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Knuci9zM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/PublishAdvanced.png)

然后，点击发布，瞧！您有了一个新的数据库，其中包含了所有的表，并且随时可以使用！

#### 模式比较工具

正如您可能猜到的，Schema compare 是您每天都会使用的工具，用于比较您的 SQL 代码和数据库，并应用这些更改。

[![](img/d047d9e9ed0156846f8827e0d6ef2a9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hE9bgp3n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SchemaCompareOption.png)

一旦到了那里，你不仅能看到什么对象将被改变，还能看到改变是什么。

[![](img/132e3b10d97000ff3379a0cea9ee7585.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bm9MbHKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SchemaCompareDifferences.png)

[![](img/767690451bcfcd6695944a485e61dc6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MFvwDS1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/tech-posts/mastimg/working-with-stored-procedures/SchemaCompareDifferencesDetails.png)

在点击“更新”之前，您可能希望进入模式比较选项并取消选中“阻止可能的数据丢失”，因为如果您希望应用在另一个分支中所做的更改，您可能希望接受数据将会丢失。

#### proupdater

模式比较在他的工作中是完美的，它可以从头创建一个数据库，创建用户，模式，处理表和列重命名，一切。但是它也会减缓你的开发过程。比较步骤可能需要 3 分钟，更新本身也需要 3 分钟，如果你一天做 10 次，你会浪费一个小时等待数据库同步。

于是我创建了一个叫 [ProcUpdater](https://github.com/kblok/ProcUpdater) 的小 app(没错，那天我就有了灵感)。ProcUpdate 将监视您的存储过程文件夹，并立即应用您的存储过程更改。当您切换分支时，它甚至可以应用所有存储过程的更改。它并不完美，它不会应用表格更改，但它可以每天为您节省 1 个小时！

### 词

如果您需要在 CI 环境中运行集成测试，这个工作流非常适合。如果您使用基于 Windows 的 CI 环境，如 [AppVeyor](https://www.appveyor.com/) ，您将能够在每次构建分支时在该环境中部署您的数据库。你只需要运行`msdeploy.exe`。可能是这样的:

```
msbuild "NorthwindDB.sqlproj" /p:Configuration=Release /p:Platform="Any CPU"
"C:\Program Files\IIS\Microsoft Web Deploy V3\msdeploy.exe" -verb:sync -source:dbDacFx="NorthwindDB.dacpac" -dest:dbDacFx="...",dacpacAction=Deploy,CreateNewDatabase=True' 
```

Enter fullscreen mode Exit fullscreen mode

### 最后的话

我过去常说，大多数时候你可以隐藏复杂性，但不能完全消除它。如果您的项目使用了大量的存储过程，我们无法消除这种复杂性(我们甚至还没有谈到如何处理暂存环境)，但是我们可以加快这个过程，让您的团队更有效率。