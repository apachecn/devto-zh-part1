# Git:重置基础

> 原文：<https://dev.to/ricardomol/git-rebasing-d98>

来源:[https://dev . to/Maxwell _ dev/the-git-rebase-introduction-I-wish-id-had](https://dev.to/maxwell_dev/the-git-rebase-introduction-i-wish-id-had)

> 假设当您从主分支创建分支时，主分支处于提交#1。分支中的每个提交都位于提交#1 之上。当您准备合并您的分支时，您发现其他开发人员进行了更改，并且最近的提交是提交#5。
> 
> 重定基础是获取分支的所有提交，并将它们添加到提交#5 而不是提交#1 之上。如果您将提交#1 视为您的分支的“基础”,那么您正在将该基础更改为最新的基础，即提交#5。因此，为什么它被称为 rebasing！

> 一个简单的 rebase 有一个非常简单的命令结构:`git rebase <branch>`。布兰奇是你要放弃的人。所以在这里，您将运行 git rebase master。假设没有冲突，这就是 rebase 所需要的！
> 
> rebase 本身在技术上删除了您的旧提交，并使新提交与它们相同，重写了 repo 的提交历史。这意味着将重组推至远程回购将需要一些额外的动力。使用 git push - force 可以很好地完成这个任务，但是更安全的选择是 git push - force-with-lease。后者会提醒你任何你没有注意到的上游变化，并阻止这种推动。这样你可以避免覆盖其他人的工作，所以这是更安全的选择。

> 如果您以前处理过合并冲突，那么重定基冲突的处理方式基本相同。运行 git status 将告诉您冲突在哪里，两个冲突的代码段将紧挨着，这样您就可以决定如何修复它们。
> 
> 一旦一切都解决了，就像普通的合并冲突一样添加并提交更改。然后运行 git rebase - continue，这样 git 就可以对其余的提交进行 rebase。它会暂停任何更多的冲突，一旦他们设置好了，你只需要用租赁来推动。
> 
> 您还可以使用两个较少使用的选项。一个是 git rebase - abort，它会将您带回到开始 rebase 之前。这对于你不能仓促做出决定的意外冲突很有用。另一个是 git rebase - skip，它完全跳过了导致冲突的提交。除非这是一个不必要的提交，并且你觉得很懒，否则你可能不会经常使用它。