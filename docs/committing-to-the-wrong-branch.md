# 犯了错误的分支

> 原文：<https://dev.to/gonedark/committing-to-the-wrong-branch>

最近 [CodeRabbi](https://twitter.com/coderabbi) 在推特上发布了一些 Git 别名。我们都知道[化名](https://dev.to/gonedark/stop-aliasing-core-git-commands)的立场。尽管如此，[他最近的推文](https://twitter.com/coderabbi/status/904708039533584387)收到了许多提出不同解决方案的回复。

这导致了 Git 的陡峭的学习曲线——做某事的正确方法是什么？我试图通过展示 Git 命令的不同使用方式来解决[获取 Git](https://gettinggit.com) 的问题。

在这种情况下，创建了一个别名`move`来解决将工作提交到不正确的分支的问题。它通过运行以下命令来实现这一点。

```
MESSAGE=$(git log -1 HEAD --pretty=format:%s)
git reset HEAD~ --soft
git stash
git checkout destination-branch
git stash pop
git add .
git commit -m $MESSAGE 
```

Enter fullscreen mode Exit fullscreen mode

正如答复中提到的，还有其他方法可以解决这个问题。

在探索替代解决方案之前，我想先解决这个问题的模糊性。为了确定*合适的*解决方案，我们需要回答几个问题:

1.  *目的地*分支是否存在或者是否需要创建？
2.  我是否提交了*不属于*目的地*分支的工作？*
3.  我犯了多少次*错误*？

对我来说，*适当的*意味着使用有副作用的命令，所以我花费最少的努力。

根据这个定义，我立即排除了使用`stash`。虽然`stash`是一个有用的 Git 命令，但它非常微妙。例如，它*在索引中隐藏*所有东西，但不包括未被跟踪的文件。这可能不是你想要的。也许你只是想要阶段性的改变。也许你想要未被追踪的文件。

也没有必要仅仅为了在另一个分支上重新提交而重置提交。假设您希望提交*原样提交*。你只是走错了方向。

让我们看看一些替代解决方案。

## 结账+复位

一个简单的解决方案是从您当前的分支创建另一个分支。它将具有相同的提交历史，因此包含不正确的提交。这允许您从当前分支中删除提交。然后您可以签出新的分支并完成您的工作。

```
git branch destination-branch
git reset --hard HEAD~1
git checkout destination-branch 
```

Enter fullscreen mode Exit fullscreen mode

### 假设

这使得假设目的地分支*不存在*。它还假设所有以前的提交都属于目标分支。如果这些假设都不成立，使用`cherry-pick`解决方案。

这还假设您只错误地提交了一次*。如果你做得更多，相应地增加相对参考(例如`HEAD~2`、`HEAD~3`等)*

 *## 精选

另一种解决方法是使用`cherry-pick`。如上所述，当提交历史不同或者目标分支已经存在时，可以使用*挑选*。

首先，创建并签出目标分支。您将第二个参数传递给`checkout`来引用分支点，因为分支没有相同的提交历史。然后您可以`cherry-pick`不正确的提交。完成后，您可以切换回前一个分支并重置不正确的提交。

```
git checkout -b destination-branch good-reference
git cherry-pick 12345
git checkout -
git reset --hard HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

### 假设

这假设目的地分支*不存在*。如果是，将第一个命令改为:`git checkout destination-branch`。

与其他解决方案一样，如果您不正确地提交了不止一次，那么您将需要为每个不正确的提交运行`cherry-pick`。你也可以从 Git 1.7.2 开始传递一个范围。例如，如果`eddd21`引用了您的第一个错误提交，而`7e6802`引用了最后一个错误提交，您可以运行:`git cherry-pick eddd21^..7e6802`。樱桃采摘系列有其自身的细微差别。我经常发现我只是运行单独的`cherry-pick`命令。

## 按压+复位

在某些情况下，您可以简单地将当前分支推送到不同名称的远程分支。然后重置本地分支以删除不正确的提交。

```
git push origin HEAD:destination-branch
git reset --hard HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

### 假设

这假设您正在使用远程分支。它还假设提交历史是相同的，但是目标分支是否存在并不重要。

与其他解决方案一样，调整在`reset`命令中使用的相对引用来删除适当数量的提交。

## 结论

您可以理解为什么学习和使用 Git 会很有挑战性。对于这个问题，至少有四种不同的解决方案。虽然我提倡保持命令简单的解决方案，但我希望通过演示所有的解决方案，您可以了解何时应用每一个。

***想每天看到更多 Git 场景？**除了学习核心命令之外，[获取 Git](https://gettinggit.com) 视频系列还展示了您将用来解决 Git 日常问题的命令。**