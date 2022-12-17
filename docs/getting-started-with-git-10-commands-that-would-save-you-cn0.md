# Git 入门？10 个命令可以拯救你…

> 原文：<https://dev.to/jessicaiwu/getting-started-with-git-10-commands-that-would-save-you-cn0>

在许多其他问题中，推送、拉取、修复合并问题曾经是我无法解决的问题，因为我不熟悉 git。我总是害怕我会破坏一些东西，导致我的代码中断或者完全消失。理解这些过程是如何进行的需要一段时间。在掌握了一些之后，我想我应该分享这些知识。

如果您还不知道为什么 Git 很重要，请记住这一点:

> Git 是一个免费的开源分布式版本控制系统，旨在快速高效地处理从小到大的项目。
> ~[https://git-scm.com](https://git-scm.com)

我将谈论 10 个 Git 命令，它们将帮助你有效地工作。

#### **1 .去启动**

这基本上是你的起点。您需要在系统上选择一个位置，用作您的本地 Git 存储库。“init”命令会为您创建一个新的 git 存储库。如果您已经有一个现有的项目，“init”会在您的工作目录中创建一个 git 存储库。在您的终端上，cd 进入您的项目所在的目录路径，并键入 git init。

```
 $ cd var/www/devProj
  $ git init 
```

Enter fullscreen mode Exit fullscreen mode

#### **2。git 克隆**

如果您在一个团队中工作，并且您需要在一个已经存在的存储库上工作，您可能需要克隆它以便在您的本地有一个版本。Git clone 只是复制现有存储库的内容，并在新的目录中重新生成它。在您的终端上，cd 到您希望项目所在的目录路径，并在那里克隆它。

```
 $ cd var/www/
  $ git clone path/to/the/repo 
```

Enter fullscreen mode Exit fullscreen mode

#### **3 .去添加**

如果您一直在处理一个分支，则需要将您一直在处理的文件添加到索引中，然后才能准备提交。您可以使用`git add`添加一个特定的文件，也可以使用`git add .`一次添加所有新的和修改过的文件，只要有新的更改，您可以在提交之前多次添加。

```
 $ git add /name/of/file
    $ git add . //new and modified files 
```

Enter fullscreen mode Exit fullscreen mode

#### **4 .去委员会**

添加完所有更改后，您可以将它们存放起来进行提交。这将更新头部，但不会更新远程存储库。每次你使用`git commit`时，你所做的是整理你之前使用`git add .`记录的所有文件变化，并把它变成一个对象。

```
 $ git commit -m "write-a-commit-message"

      //You can actually add and commit at once using -

    $ git commit -am "write-a-commit-message" 
```

Enter fullscreen mode Exit fullscreen mode

#### **5 .去分支机构**

Git branch 显示了存储库中所有可用分支的列表。它还显示了你目前在哪个分支。分支是项目的一部分，独立于所有其他分支。您的第一次提交通常是针对 Git 的默认分支`master`。如果需要创建新的分支，可以使用 git branch new-branch。这个新分支将独立于所有其他现有分支，直到它被合并到另一个分支中。

```
 $ git branch branch-name
      $ git branch -D branch-name //This deletes the created branch 
```

Enter fullscreen mode Exit fullscreen mode

#### **6\. git checkout**

当你想离开一个分支机构时，Git 结帐是必要的。回想一下，git branch 只创建了一个新的分支，然而，git checkout 将您移动到一个已经存在的分支。

```
 $ git checkout branch-name //This switches you to another branch
      $ git checkout -b new-branch-name //This creates a branch and checks out to 
         it also. 
```

Enter fullscreen mode Exit fullscreen mode

#### **7\. git status**

这个命令告诉您当前在哪个分支上。它标识了所有已经完成的变更，以及所有还没有被添加和提交到那个分支的变更。

```
 $ git status 
```

Enter fullscreen mode Exit fullscreen mode

#### **8、去日志**

它列出了该分支上所有以前的提交、作者、提交的时间和日期。这有助于你跟踪你的活动。

```
 $ git log 
```

Enter fullscreen mode Exit fullscreen mode

#### **9 .去推**

如果有几个人在一个项目上工作，那么其他几个人肯定需要处理您刚刚在一个分支上所做的新变更。为了让他们访问您的更改，您需要将您的提交推送到远程存储库。这样，远程版本将被更新，并且可以被其他人容易地访问。

```
 $ git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

#### 10。git pull

作为一名开发人员，您可能需要从几个存储库中提取数据。“拉”从远程源获取文件，并将它们合并到本地文件中。这样，您就可以访问与远程存储库相同的文件内容。

```
 $ git pull origin master 
```

Enter fullscreen mode Exit fullscreen mode

还有许多其他 git 命令，通常，您会在需要的时候找到它们。然而，上面列出的 git 命令肯定会对您有所帮助，尤其是如果您刚刚熟悉 git 的话。

没有人知道这一切，所以，如果你有其他的命令想分享，请不要犹豫，把它们作为评论。
如果你喜欢文章，请点赞并分享。