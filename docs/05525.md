# Git 助手——简化您的 git 工作流程

> 原文：<https://dev.to/gyandeeps/git-helpers---simplify-your-git-workflow>

*此为原帖[此处](https://gyandeeps.com/git-helpers/)T3】*

作为一个 git 爱好者，我厌倦了一遍又一遍地输入相同的命令。这促使我为我每天使用的 git 工作流构建一些别名。大部分别名都是从尼古拉斯·c·扎卡斯的要旨中继承来的。我进一步创造了更多的别名，每次我为像 [Eslint](http://eslint.org/) 这样的开源项目做贡献时都会用到这些别名。

## 命令

所有命令的实际代码是[这里是](https://gist.github.com/gyandeeps/c7872187585de4fa7153)

### 处理一个问题

当您计划处理开源项目中的一个问题时，您会希望在项目中创建一个新的分支，然后开始处理它。为此你必须经历几个步骤。要在您项目上对流程进行流水线操作，您只需运行命令:

```
ws 34 
```

Enter fullscreen mode Exit fullscreen mode

该命令将使用来自远程的更新来更新主分支，然后在其上创建一个名为`issue34`的新分支。

### 工作完成后移除一个分支

一旦你完成了分行的工作。然后，您可以简单地运行下面的命令，从本地和远程 git 中删除分支。

```
wd 34 
```

Enter fullscreen mode Exit fullscreen mode

### 用主数据更新您的分支机构(所有更改已提交)

比方说，在您的分支上工作之后，您想要用来自主控器的最新内容更新您的分支。这在您想要用来自`master`分支的最新内容重新构建您的分支的场景中很重要。

```
update 
```

Enter fullscreen mode Exit fullscreen mode

### 用 master 更新您的分支(未提交的更改)

比方说，在您的分支上工作之后，您想要用来自主控器的最新内容更新您的分支。但此时您尚未提交您的更改。使用`updateD`，它将存储您的更改，然后从 master 更新您的分支，然后从存储中选择更改，并将其应用到当前分支。

```
updateD 
```

Enter fullscreen mode Exit fullscreen mode

### 粉碎你的提交

我总是喜欢通过压缩我的提交来保持我的更改历史干净。一旦您在一个分支上工作，并且您想要挤压您的提交，简单地运行下面的命令来重设基础。您可以从头部传入一个参数，说明您希望在 rebase 中包含多少个提交。默认情况下，它将在最后两次提交时运行 rebase。

```
rebase 4 # This will rebase on last 4 commits, default is 2 
```

Enter fullscreen mode Exit fullscreen mode

### 将您的更改推送到远程

完成工作后，您希望将更改推送到远程。在这里，你通常会做常规的`push`，但是如果你已经在你的分支上做了一些 rebases，那么你唯一的选择就是`force push`。我们使用`--force-with-lease`国旗只是为了更安全一点。

常规推送:

```
push # This will push your branch changes but will not push on master 
```

Enter fullscreen mode Exit fullscreen mode

强制推送:

```
fpush # This will push your branch changes but will not push on master 
```

Enter fullscreen mode Exit fullscreen mode

这两个命令都不允许您将其推送到 master，因为这被认为是一个好的实践，即总是分支您的更改，然后使用 PR 将其合并到 master。这完全是我的观点。

### 从原点拉动变化

运行该命令将从上游提取变更，并将它们合并到当前分支。它使用`--rebase --prune`标志。

```
pull 
```

Enter fullscreen mode Exit fullscreen mode

### 更新分叉

从上游回购更新分叉的快速方法。它将转到主服务器分支，获取上游主服务器，将上游主服务器合并到本地主服务器，然后将主服务器推送到原始主服务器。这是一个完整的循环。

```
uf 
```

Enter fullscreen mode Exit fullscreen mode

它希望您在运行该命令之前设置`upstream`。

### 快速提交

通过提供提交消息来提交更改的快速方法。Itt 将使用`-a`标志添加当前更改的文件。

```
c "<message>" 
```

Enter fullscreen mode Exit fullscreen mode

### 记录提交

在一行中快速简单地查看提交日志。它的格式可以让你看到提交信息、日期和作者以及其他信息。

```
log 
```

Enter fullscreen mode Exit fullscreen mode

### Git status

获取分支的当前状态。

```
s 
```

Enter fullscreen mode Exit fullscreen mode

## 设置

要使用所有这些命令，请按照以下步骤进行设置:

1.  转到您的用户目录，对于 windows 用户将是`C:\Users\<your user>`。
2.  如果没有文件，创建一个`.bashrc`文件。
3.  将以下要点的内容添加/附加到该文件中。
4.  打开 bash 窗口。如果已经打开，那么关闭它，然后重新打开 bash 窗口。
5.  运行`alias`命令，您应该会看到所有已定义别名的列表。

**注:**根据您的需要，随时更改功能。

## 为上述所有命令的要旨

[点击此处查看要点](https://gist.github.com/gyandeeps/c7872187585de4fa7153)

我希望这能帮助人们简化他们的 git 工作流程。请分享你的建议。