# 如何吃得好

> 原文：<https://dev.to/andrew565/how-to-git-well>

向任何方向扔一块石头，您将会遇到另一个 Git 工作流。这是我的，基于和 Git 一起工作了 10 多年。它利用了`git rebase`，所以如果你害怕重置，现在就离开(然后去[阅读这个](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)，并意识到它只不过是另一个工具)。额外收获:也一定要看看[我的便利 git 助手](https://andrewscripts.wordpress.com/2017/06/08/my-favorite-git-aliases-functions-and-configs/)！

1.  找到您的`origin`回购
2.  派生原始回购以创建您自己的回购
    *   如果使用 Bitbucket，它将自动设置您的回购与`origin`同步，除非您取消选中“保持同步”
3.  将您的 fork 克隆到您的本地机器上，这里`origin`将引用您的 fork。
4.  从您的本地主机创建一个特征分支
    *   `git checkout –b feature-branch-name`用你想给你的分支取的名字替换“特征-分支名称”
    *   良好的实践:使你的分支名称独特，并具体到你正在处理的任务或功能。`branch-1`不是一个有用的分支名称，`time-travel-feature`比较好，`adding-unlimited-undo-in-cms`最好。
    *   可选:使用`git push –u origin feature-branch-name`将新的、空的特性分支推到 fork 的远程端点。这就建立了“跟踪”,所以每次你向前推的时候，你都不需要指定哪里，你做一个`git push`,它就已经知道要推到哪里了。与`git pull`相同。
5.  做你的工作
    *   尽早并经常承诺。我建议每次你要“完成”或者至少工作的时候提交，但是任何时候你需要转换任务或者想要一个你正在工作的路点的时候提交 WIP(工作进行中)。
6.  如果你还没有“最终”提交，那么就提交吧。
7.  将您的特征重新基于当前版本的母版(这样可以减少合并冲突)
    *   `git rebase origin/master`
    *   `git rebase -i origin/master`如果你想变得有趣，选择交互提交
8.  修复任何合并冲突(如果有)。
    *   修理，再修理一些，再修理
    *   `git add .`或`git add /file(s)/that/were/changed`
    *   `git rebase -–continue`
9.  解决任何冲突后，再次向上推您的功能分支，强制您的遥控器覆盖已经存在的任何内容(因为重新设置基础将更改分支的历史记录，从而更改其 SHA 校验和)。
    *   `git push –f`
10.  创建一个从您的功能分支到原始`origin` repo 的主分支的拉请求。
    *   即`fork/feature-branch-name -> origin/master`
11.  休息，反思你已经完成的事情，然后从第 4 步重新开始。

这个循环看起来是这样的:

`OriginMaster -> ForkMaster -> LocalMaster -> LocalFeatureBranch -> ForkFeatureBranch -> OriginMaster`

如果您使用 Bitbucket，Bitbucket 将自动同步 OriginMaster 和您的 ForkMaster。然后，您必须从 ForkMaster 下载任何更新到您的 LocalMaster。然后，您切换到您的 LocalFeatureBranch，并根据 LocalMaster 中的最新更改对其进行重新设置。然后向上推至 ForkFeatureBranch，在那里创建一个到 OriginMaster 的 Pull 请求，循环再次开始。

**良好实践**:任何时候，当“原始”回购主分支中有重大金额的合并时，最好更新您的本地主分支，并为您的特征分支重新设定基准。您越是将您的要素分支与最新的更改保持同步，合并冲突就越容易解决，并且您在工作时需要处理的意外就越少。

如果您还没有查看，请点击这里查看我的便捷 git 助手，它们使这个工作流程变得超级简单、快速和直观(从命令行)。