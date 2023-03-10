# 放弃 git GUI 习惯

> 原文：<https://dev.to/pitermarx/quitting-a-git-gui-habit-14ia>

我是 source tree 的用户，但是它让我失望了很多次(崩溃，高内存使用率)。

我已经意识到我对 git 真的了解不多。我至少应该学习我最常用的命令。

[![](img/2891399c8080d409ffa720bfd94576f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2HgRhIVp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pitermarx.com/wp-content/uploads/2020/04/0aYhAWTgPt1Ama_O2.png)

我是 windows 用户，所以 [posh-git](https://github.com/dahlbyk/posh-git) 是必须的。有针对 git 的[优秀文档](https://git-scm.com/docs)，但是它们可能有点令人不知所措，并且有很多 90%的时间都不需要的细节。这就是我的 7 项 git 快速启动清单:

1.  [git checkout](https://git-scm.com/docs/git-checkout)

切换分支的最基本命令。您可以使用-f 来丢弃任何本地更改

1.  [git fetch](https://git-scm.com/docs/git-fetch)

用于更新远程状态。知道我是否需要拉很有用。使用-p 删除远程服务器上不再存在的任何远程跟踪引用

1.  [git pull](https://git-scm.com/docs/git-pull)

从原点提取最新提交。使用-r 对尚未推送的更改进行调整

1.  [去添加](https://git-scm.com/docs/git-add)

将更改添加到临时区域。使用-a 添加所有更改。

1.  [去委员会【t1-m】](https://git-scm.com/docs/git-commit)

提交当前暂存的更改。在提交之前，使用-a 暂存所有更改。

1.  [去推](https://git-scm.com/docs/git-push)

当前提交的推送已更改为原点。

1.  其他东西

我已经在这 7 项上作弊了，但是现在我开始使用较少的命令。 [git stash](https://git-scm.com/docs/git-stash) 、 [git merge](https://git-scm.com/docs/git-merge) 、git commit with amend、git push with force 都是需要了解的好工具。

我无法理解这种差异。我仍然使用 GUI 工具来处理冲突，并在提交之前检查变更。

就是这样。这个列表应该足以让我在大多数时候不用 GUI 与 git 交互。