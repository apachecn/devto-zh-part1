# 自动 Git 标记

> 原文：<https://dev.to/funkysi1701/automatic-git-tagging-3cmo>

git 的一个特性是能够用标签标记我的变更历史中的一个点。一段时间以来，每当我发布一个版本时，我都会手动标记我的代码，所以我可以很容易地通过在两个标记之间做一个比较来找出发生了什么变化。

现在，我正在使用 TeamCity 自动化我的发布过程，我正在考虑如何更好地管理我的标签。

TeamCity 有一个叫做 VCS 标签的设置，非常方便。[![](img/07be2296b09651fa19fc578a6ef200ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7tMRwBwJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2016/06/Untitled.jpg%3Fw%3D1595%26ssl%3D1)

配置它相当简单，因为它只有三个设置。

**VCS 根标签**:这显然是你的 git 库的 URL**标签模式**:这是要添加的标签的文本。**只标记成功的构建**:如果构建失败，你真的想添加一个标签吗？

一个标签需要有一个唯一的名称，所以添加一个名为 **deployed** 的标签是行不通的。当我手动添加标签时，我使用了**deployedyyymmdd**的命名约定。虽然这个命名约定在 TeamCity 中是可行的，但是我使用了一个稍微复杂一点的东西来提供关于已经部署了什么的更多信息。

TeamCity 提供了许多参数，可以在您的构建步骤中使用，也可以在标签模式框中使用。我开始使用**deployed-build-% system . build . number %**作为我的标签，它只是用 TeamCity 构建号标记 git。

当我运行 TeamCity 部署时，我不总是使用相同的配置选项，我在本地部署，部署到测试服务器或生产，有时我只部署前端或后端。将这些信息包含在标签文本中会有多酷？

下一步是将我的标记模式更改为**deployed-build-% system . build . number %-% ServerName %-% DatabaseName %-% frontend path %**，这将添加后端数据库配置设置和前端部署到的路径。现在，当查看 git 时，您可以看到用多个标签标记的提交，每个标签对应一个成功的部署，标签将指示在该部署期间使用的设置。

现在我永远不会忘记在发布后添加标签，因为添加标签是部署过程的一部分，如果部署失败，标签将不会被添加。我可以测试我的部署以进行测试，git 将显示这是否成功，当我实际部署时，这也会显示出来。

你如何使用标签？你用标签标记成功的构建吗？为什么不让我知道或在下面留下评论。