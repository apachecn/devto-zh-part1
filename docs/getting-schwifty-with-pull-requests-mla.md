# 获取拉取请求的灵活性

> 原文：<https://dev.to/satansdeer/getting-schwifty-with-pull-requests-mla>

有一次我在制作一部大型电影。一切都很顺利，直到我忘乎所以，开始在一个分支中提交所有东西，而不是分成多个更小的分支。结果，我得到了一份巨大的公关，没有人能够正确地审阅。

让我们看看你如何避免陷入这种情况，如果你已经这样做了，该怎么做。

首先，做一些计划，把任务分成几部分。这是您能够避免创建这个巨大的拉请求的唯一方法。

然后你有两个选择，用这两个选择你可以创建小的公关，并逐步改进:

*   合并公关，以掌握早期和经常。
*   有一个长期运行的功能公关与小审查儿童公关。

区别在于你以什么为基础。在第一种情况下，你将它们基于`master`，在第二种情况下，你将它们基于一个特征分支。

## 合并早且经常

这是**首选**。不过，你最好对你要做的事情有个好的计划。您不希望最终用不与任何东西交互的孤立组件创建无意义的拉请求。比如没人用的模型。

作为增量改进来做。在你的第一次公关设置基础结构，然后通过添加更多的组件和升级旧的组件来逐步改善它。

## 长时间运行特性 PR

你必须用 master 来改变基本分支的基础，然后再改变其子分支的基础。

如果您将与其他人并行工作，这种方法可能需要在分支之间进行一些挑选。

不要忘记为每一个新功能做内务和创建小的公关，否则你会以…

## 最坏的情况

它开始作为一个长期运行的功能公关方法。但后来我有点懈怠了家务，开始在一个儿童 PRs 中提交所有东西。

最终我完成了一个巨大的 PR，有 100 多条提交和 1.5k 行代码。

这是一个非常糟糕的情况。即使有人敢于回顾它，他也更有可能忽略一些东西。

因此，如果你发现自己处于类似的情况，你需要对这份公关做些什么，使其可以审查。例如，您可以…

## 将其拆分成一叠 PRs

在寻找解决方案时，我找到了 Grayson Koonce 的这篇博客文章，他建议关闭原始 PR，将其更改复制到一个新分支，然后创建一个包含增量更改的依赖 PR 堆栈。

## 第一步

首先，您想要基于`master`创建一个新的分支，其中包含来自那个大型 PR 的所有变更。

```
$ git checkout master
$ git checkout -b stacked-pr-example-1 branch-of-that-huge-pr
$ git reset master
$ git status 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将在未提交的状态下看到该大型 PR 中引入的所有更改。您可以开始提交更改并制作堆叠的 pr。

## 先拉请求

您必须使用`git add --patch`向提交添加特定的更改。我有一个关于这个命令的[帖子。](https://maksimivanov.com/posts/git-add-patch)

但是如果你有未分级的新文件——git 不能这样添加它们。要修复它，请将`git add -N`或`--intent-to-add`应用到您希望在提交中看到的文件。这将告诉 git 添加文件本身，而不是它的内容。之后，您将能够轻松地从该文件添加行。

此时，您可能想要检查已经添加了多少个更改的行。为此使用`git diff --staged --stat`。

一旦你准备好了所有需要的改变，把其他的都藏起来，然后验证它是否还能工作。

```
$ git stash --include-untracked --keep-index 
```

Enter fullscreen mode Exit fullscreen mode

`--keep-index`将使其仅存储尚未添加的文件，而`--include-untracked`将允许其存储未跟踪的文件。

是时候提交变更并创建 PR 了。

```
$ git commit -m "Make some change to x"
$ git push -u origin stacked-pr-example-1 
```

Enter fullscreen mode Exit fullscreen mode

有一个很好的 CLI 工具 [hub](https://github.com/github/hub) 可以让我们更容易地创建 PRs:

```
$ hub pull-request 
```

Enter fullscreen mode Exit fullscreen mode

它将打开默认编辑器，您将能够添加公关描述。

## 下一个拉取请求

现在，为你的下一次公关创建分支，并弹出隐藏回来:

```
$ git checkout -b stacked-pr-example-2
$ git stash pop 
```

Enter fullscreen mode Exit fullscreen mode

重复`add`、`stash`、`commit`步骤。

现在创建新的 PR，但是要确保它是基于带有-b 标志的前一个分支:

```
hub pull-request -b stacked-pr-example-1 
```

Enter fullscreen mode Exit fullscreen mode

重复该过程，直到更改被打包到 PRs 中。

## 审核完你的 PRs 后

在这里，Grayson 给出了一个重要的提示，通过`git merge`而不是`git rebase`传播额外的提交。这样，您将能够一次解决多个提交中的冲突。

```
$ git checkout stacked-pr-example-2
$ git commit -am "Addressed this feedback"
$ git commit -am "Addressed that feedback"
$ git push stacked-pr-example-2

$ git checkout stacked-pr-example-3
$ git merge stacked-pr-example-2 # integrate changes from stacked-pr-example-2 
```

Enter fullscreen mode Exit fullscreen mode

现在，在您应用了所有反馈并使您的 PRs 获得批准后——是时候进行堆叠了。

由于 Github [自 2016 年 8 月起支持更新基础分支](https://github.com/blog/2224-change-the-base-branch-of-a-pull-request)，你可以任意顺序合并你的栈。