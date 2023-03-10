# git Wrecked——在不破坏东西的情况下保持分支同步

> 原文：<https://dev.to/rpalo/git-wrecked--keeping-your-branches-synced-without-breaking-things-4p6>

上个月，我参加了数字海洋的 [Hacktoberfest](https://hacktoberfest.digitalocean.com/) 活动，感觉棒极了。它帮助我鼓起勇气为开源软件做贡献。这绝对是一次很棒的经历(我还因此得到了一件 t 恤😬).这真的是我第一次与[分叉 Git 工作流](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow)互动，我不确定该做什么。我在这里和那里看到了一些关于应该如何做的片段，但是除了本地/个人 git 所需的基本的`push, pull, add, commit`命令之外，我对那些*或* git 命令还不够熟悉。所以我想分享一个故事和我学到的东西。

## 分叉 GUI

为了这篇文章，我正在对一个自述文件做一些补充。我已经完成了回购，在我的机器上克隆了一个本地版本，为修复创建了一个分支，做了一些初始更改，推回到 Github，并打开了一个 pull 请求。到目前为止，一切顺利。我觉得很有成就感。

[![I love it when a plan comes together](img/a078274635b25f7342a5b7e372b11822.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uh6ZR_qp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/git-plan.gif)

几天后，维护者给了我一些想法和可能的修改。太好了！这是真正的合作！太令人兴奋了！我回到我的本地回购，但我注意到原来的上游回购已经做了一些改变。听说让您的本地回购和分支机构保持最新是一个好主意，我决定尝试刷新一切…但我真的不知道如何做。我环顾四周，但是我被官方的 git 文档吓住了。这时我想起我有一个非常方便的 git GUI 应用程序， [GitKraken](https://www.gitkraken.com/) 。我打开它，点击了几下，我就更新了我的`origin`和本地分支，做了我想做的快速编辑，并把所有东西都上传到 GitHub。至少我是这么认为的。

那天晚上，我收到了维护者的一条消息，询问我添加到拉取请求中的这些额外文件是什么。让我感到恐惧的是，当我查看我的 GitHub repo 的提交历史时，它是混乱的，我甚至在我的 pull 请求分支更改中加入了其他人从`upstream/master`提交的内容。我非常困惑——也感到羞愧。不仅我在“做 git”上搞砸了，而且其他人(可能还有很多人)也看到了我这样做。

[![Oh no.](img/fd27324f87c77573bc6f9cf95ae6f8ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ublTHaac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/git-oh-no.png)

不要误解我。GitKraken 是一个神奇的工具，非常漂亮，非常强大。我只是认为，我个人应该只使用它来检查项目，而不是使用它的编辑功能。在命令行上，当您键入 git 命令时，您知道您正在更改内容，这是一件大事。在图形用户界面中，如果你不知道你在做什么，他们可以很容易地点击一些按钮，做很多潜在危险的事情，而我习惯了图形用户界面中的按钮几乎总是低风险和可撤销的。我确实需要额外的思考时间来输入 git 命令。

但不管怎样，这让我想到了这篇文章的重要部分:

## 正道

本节的目标是以正确的方式(或者至少是一种正确的方式)列出分叉 Git 工作流的每一步，以及相关的命令，以便未来的我在不可避免地忘记时可以回来阅读。我将把这个展示出来，但是如果你看到我做错了或者可以做得更好的地方，请一定让我知道，我会尽量用你分享的最佳实践来更新这个。

```
# This is assuming you've already forked the Upstream Repo
# on GitHub/BitBucket/GitLab/etc.

# Clone to get an initial local copy.
git clone <url-to-your-fork>

# Add a remote link to the upstream repo
git remote add upstream <url-to-upstream-repo>

# Create a new branch for the feature/fix that you're doing
git checkout -b <new-branch-name>

# Do some work, edit some files
git add <files>
git commit -m <your-commit-message> 
```

哦不！对`upstream`和/或您的`origin/master`和/或您的`origin/<branch-name>`进行了更改，而这些更改不在您的本地机器上。

```
# Inspect the changes made to origin
git fetch origin

# Update your branch
git merge origin/<branch-name>

# Update your master
git checkout master
git merge origin/master

# Inspect the changes made to upstream
git fetch upstream

# If you don't have any commits on master ahead of upstream,
# you can just pull the upstream changes in
git checkout master
git merge upstream/master

# If your master branch is ahead of upstream, you may want to rebase
# In order to "work on top of" everyone else's changes, instead of merging
git rebase upstream/master

# Now that your master branch is up to date, you can decide whether
# or not you want to rebase your feature branch onto the new
# tip of your master branch.  Generally a good idea.
git checkout <your-feature-branch>
git rebase master

# OR, if you're about to submit a pull request and you  want to
# clean up your commit history, do an interactive rebase and
# squash and otherwise clean your commits/messages
git rebase --interactive master

# Finally, push your updated branch
git push -u origin <your-feature-branch>

# Sometimes, rebasing rewrites history, and pushing won't work.
# Try --force-with-lease, which is the safe way to force the push
# through.  More on that later
git push origin <your-feature-branch> --force-with-lease

# Optionally, push everything to update your origin/master too
git push origin --all 
```

## 什么是 Git Rebase

Git Rebase 把我搞糊涂了。我不知道我能提供比[亚特兰蒂斯教程](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)更好/更有画面感的解释。这是给特别懒的人的简短版本。

1.  您的特征分支在某个点从主特征分支，该点称为“基础”。
2.  重定基础将这个“基础”指向一个新的地方，通常将它向前移动到另一个分支的新主管。
3.  在此过程中，您可以改写提交消息、重新排序提交、删除提交和一起挤压提交。
4.  一般来说，你永远不应该对已经发布到公共场合的东西进行重新调整，*尤其是*如果有可能其他人已经开始对你重新调整的东西进行工作。尝试只在您的本地系统上重置*。*
5.  在拉请求过程中，一旦提交了初始请求，就会要求您更新/修改原始的拉请求代码，这将向拉请求历史记录中添加多个提交。您可能会试图压缩这些内容，以保持拉取请求看起来漂亮而简洁。抵制这种冲动。经过研究，似乎 a)这样做会违反第 4 条，b)拉取请求应该是讨论的论坛/记录，如果你重写内容以隐藏所有的中间更改，任何事情都没有意义，你会错过那段历史。
6.  远程存储库不一定喜欢这种重写历史的行为。通常，一个简单的`git push`就会被拒绝。执行`git push --force`将迫使远程回购接受推送，但你可能不应该使用`--force`。安全的方法是使用`--force-with-lease`，它只允许一个不会弄乱任何人的力量。嗯，大部分时间。阅读[这篇关于强制租赁](https://developer.atlassian.com/blog/2015/04/force-with-lease/)和不先合并就获取和推送的危险的帖子。

## 总结起来

就像我开始说的，我在过去的两个星期里一直在学习这个工作流程，所以我绝不仅仅是一个初学者。希望这有所帮助，也希望比我聪明的人会让我知道我是否做错了什么。在我笨拙地完成我的贡献时，对我如此耐心的 Exercism Ruby Track 团队大声喊出来。

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com)*