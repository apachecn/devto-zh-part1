# 我每天使用的 3 个 Git 命令

> 原文：<https://dev.to/gonedark/3-git-commands-i-use-every-day>

最近我更加关注我运行的 Git 命令来帮助脚本[获取 Git](https://gettinggit.com) 。在这一点上，我有如此多的材料，我被告知把其中的一些变成一篇博客文章。

我从演讲和[结对](https://jason.pureconcepts.net/2016/04/mentoring-pair-programming-development-coaching/)中发现，我们大多数人并不像我们希望的那样对 Git 感到舒服。它分享了开发人员真正喜欢的 Git 命令选项和工作流。

这里是我每天使用的三个 Git 命令。是的，我要跳过`git status`。不，它们不是你从未听过的 Git 命令。

## [t1:去添加-p](#1-git-add-p)

所以你一整天都在做一些改动，然后用`git add .`或`git add -A`把它们都加上去。

哦，讽刺的是。

抛开这些命令笨拙的本质不谈，您花了那么多时间精心设计更改，只是为了将它们放入 Git 存储库中。这就像准备一顿美餐，然后一下子全部塞进嘴里。

当我完成我的更改时，我更喜欢运行`git add -p`。`-p`代表*贴片*。运行此命令允许您以交互方式查看您的每个更改。这样，您可以决定是否要添加更改。

虽然比`git add .`更费时间，但是很值得。我经常会发现一些我可能不想提交的更改——注释或调试语句。`git add -p`提供最终审核，以确保我的更改已准备好提交。

## 2: git 提交-修改-不编辑

在做某件事的时候，我通常只提交一两次。然而，考虑到读取代码与编写代码的比率是 10 倍，这些提交可能会持续很长时间。在这两次提交之间，我喜欢进行小规模增量提交——即使只是一次 *WIP* 提交。

对于这些提交，我喜欢使用`git commit --amend --no-edit`。是的，我知道我可以用`git rebase`来代替。但是大多数时候工作是按顺序进行的。所以我不需要所有这些，我可以简单地将我的修改添加到前面的注释中。

这也允许我保持一个*干净的状态*，这使得运行其他 Git 命令以及跟踪我随时间的变化变得更加容易。它还提供了自由，让*在一些改变上加钉*，如果不依赖`⌘` - `z`，我可能最终不会使用这些改变。

我承认，这开始进入我认为的提交(我将在我的下一篇文章中写下)。

## 3:去复位——硬

我知道你们有些人吓坏了。有些人觉得`git reset`很可怕，尤其是在使用`--hard`选项运行时。然而，我发现它补充了其他两个命令。例如，如果有我没有用`git add -p`添加的更改，或者来自*尖峰*的更改我不想提交，我可以很容易地用`git reset --hard`丢弃它们。

现在，这并不是说你应该对`git reset --hard`漫不经心。还有一些[恐怖故事](https://www.reddit.com/r/git/comments/5hhmoi/undo_git_reset_hard_on_a_repo_with_no_commits/?)。简单地留心就会减轻这种恐惧。实际上，当配备了`git reflog`和`git fsck`时，你永远不应该害怕运行 Git 命令。

最后，我认为这 3 个 Git 命令更符合我的日常工作流程。也许你看到了他们是如何融入你自己的。在我即将发布的视频系列*获取 Git* 中，我会解释这些命令和其他 20 多个 Git 命令。现在[可以提前进入](https://gettinggit.com/)。等我提交完，看看我在[上的下一篇帖子。](https://jason.pureconcepts.net/2017/01/when-to-make-git-commit/)