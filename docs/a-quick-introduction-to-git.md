# git 的“快速”介绍

> 原文：<https://dev.to/jmourtada/a-quick-introduction-to-git>

我在学习 git 的时候写了这篇文章。如果你喜欢我的文章，看看[我的博客](https://www.mourtada.se/)。

我今年的目标之一是为一个开源项目做贡献。我为什么要那样做？我认为这将使我成为一名更好的开发人员，这可能是一种有趣的方式来做一些不同于我在工作中所做的事情。同时，我在我使用的每个项目中都使用开源软件，现在是时候回报一些东西了。开源软件的最大来源是 GitHub，我意识到我需要学习更多关于 git 的知识。这篇文章是我学习 git 的心得，希望对你有用。

## 什么是饭桶

Git 是一个分布式版本控制系统(DVCS)。它是由 Linux 社区在 2005 年创建的。目标主要是，它将是快速的，完全分布式的，对非线性分支有强大的支持，并可用于大型项目。

与像 Subversion 或 Team Foundation 版本控制(TFVC)这样的集中式版本控制系统(CVCS)不同，git 是完全分布式的。这意味着每个人都有自己完全成熟的版本，与其他版本隔离开来。您可以在不连接到中央存储库的情况下完成您的工作，创建分支并提交。但是，当您准备好与他人分享您的更改时，您可以将您所做的更改推送至中央存储库，或者如果您想要其他人提交的最新更改，只需将它们拉至您自己的存储库即可。

## 三种状态

Git 有你的文件可以驻留的树形状态，这是 git 的核心，所以记住这一点非常重要。

*   修改
*   阶段
*   坚定的

当文件发生更改时，它将被标记为已修改，但不会被提交。将提交的文件只有那些已转移的文件。当您暂存一个文件时，它将被暂存。如果在转移文件后进行了更改，这些更改将不会被提交。当文件被提交时，它们被安全地存储在存储库中，并处于版本控制之下。

[![The three areas of git. Working directory, Staging area and repository](img/06fe01c9cf2d244116904e813156e58c.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/git-areas.png)

这三个状态与树区域相关联

*   工作目录
*   临时区域/索引
*   贮藏室ˌ仓库

工作目录是您处理文件的地方。当您想要存储更改时，您需要做的第一件事是将文件放入暂存区，这样 git 就知道提交什么。既然文件已暂存，就可以提交这些文件了。请记住，如果有尚未暂存的更改，这些文件将不会被提交。

## 如何存储更改

git 和其他版本控制系统(VCS)的一个很大区别是它存储快照，而不是自上次提交以来的差异。如果您更改了文件中的一行，git 会获取该文件的完整快照，并将其与提交一起存储。为了提高效率，git 不拍摄未更改文件的快照，而是引用该文件的最后一个快照。git 存储文件的方式以及存储库的本地性是 git 如此快速的原因。

每次提交都使用 SHA-1 哈希基于其内容(文件和目录结构)进行校验和检查。这使 git 意识到了损坏，因此您不会丢失任何信息。校验和随后被用作参考，并将成为该提交的唯一 id。

[![A series of commits](img/30a521e2e78fb7ab5db4648e0929b001.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/commits-1.png)

提交时，它会将快照与一些元数据(如提交消息、时间、作者等)存储在一起。存储的一个重要内容是对一个或多个父提交的引用。提交没有对子提交的引用。记住这一点非常重要，当我们开始谈论撤销和回顾历史的时候。

## 入门指南

您需要做的第一件事是在您的系统上安装 git。[拿到这里](https://git-scm.com/downloads)。这篇文章的其余部分将在命令行上使用 git，但有一个内置的 GUI 工具可以与“git-gui”和几个特定于平台的第三方工具一起运行。

安装 git 后，我们需要设置一些首次配置。Git 既有全局设置，也有特定于存储库的设置。您的姓名和电子邮件将与每次提交相关联，并且需要进行设置。

`git config --global user.name "Jonathan M"
git config --global user.email [jonathan@example.com](mailto:jonathan@example.com)`

如果你不想在每个存储库中使用这个名字或电子邮件，我可以省略`--global`参数，它将只在当前存储库中使用。还有很多设置可以改变，比如使用默认编辑器。但是现在这已经足够了。

## 获取您的知识库

`git init`命令将在您的当前目录中创建一个空的存储库和一个名为`.git`的子目录，这是存储库的框架。`.git`文件夹保存了关于你的存储库的数据，比如配置文件，你所有的提交，分支和标签。

git 使用`git init --bare`创建了一个空存储库，它不会将`.git`文件夹创建为子目录，而是将所有信息存储在根文件夹中。一个空的存储库并不意味着在其中进行开发工作，因为它没有一个工作目录。裸存储库的目的是用作中央存储库，只存储更改，而不能编辑它们。空存储库的命名约定以“.”结尾。git”例如“my-repo.git”

更常见的情况是，已经创建了存储库，您希望在本地获得它，以便可以查看并开始使用它。

`git clone [https://github.com/Jontem/my-central-repo.git](https://github.com/Jontem/my-central-repo.git)`

将 my-central-repo(在本例中托管在 GitHub 上)复制到我当前的目录中。我的新本地存储库现在是它自己的完全独立的存储库，是克隆存储库的完整副本。当存储库被克隆时，它会创建一个名为 origin 的远程连接，指向原始存储库。在我的本地存储库中所做的每一个更改都不会反映到我的中央存储库中，除非我将我的更改推回。

[![Cloning a repository](img/16f87fb8c90ecbc0590fff6a98bb1c0a.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/cloning.png)

## 保存您的更改

当你有你想做的事情时，首先要做的就是准备好你的改变。`git add`命令将文件或目录添加到暂存区/索引中。

您可以指定哪个文件

`git add <file/directory>`

或者使用

`git add .`

指定所有文件(不是您的`.gitignore`文件)。星号也可用于选择多个文件，如`git add *.txt`。临时区域中的文件是将要提交的文件。如前所述，add 命令不仅用于向暂存区添加新文件，还用于添加已更改的文件。

请记住，当一个文件被添加到临时区域时，git 将照当时的样子拍摄该文件的快照。如果在此之后更改文件，您将不得不再次运行`git add`来暂存这些更改。

要提交临时区域中的文件，请运行

`git commit`

这将打开一个外部编辑器，以便添加提交消息。

`git commit -m "changed the readme"`

使用`-m`参数，可以直接从命令行添加消息。

`git commit -a`

如果您认为 staging 部分是一个不必要的步骤，那么有一个`-a`参数，它会将所有以前添加的文件添加到这个提交中。

## 检查您的存储库

为了查看您的工作目录、暂存区和提交的更改之间的相互比较，git 使用了 status 命令。

`git status`

通过状态命令，显示这些信息

*   你在哪个分支机构
*   要提交的更改
*   未准备提交的更改。要提交这些文件，请运行 git add
*   未被追踪的文件。存储库未跟踪的文件。要提交这些文件，请运行 git add

检查存储库的历史可以通过

`git log`

它显示了存储库中的所有提交，最新的提交位于顶部。

`git log -p -2`

这向您显示了最近的两次提交，以及一次提交引入的每个更改的差异视图。

`git log --stat`

`--stat`参数将只显示哪些文件已经改变。

`git log --pretty-format="%h - %an, %ar : %s"`

要根据您的需要定制输出，您可以使用`--pretty-format`参数。运行`git help log`寻找可用的占位符。

## 分支

在 git 中创建分支非常简单快捷。它们只是一个指向提交的指针，这与集中式版本控制非常不同，后者会将所有文件完全提取到一个新的目录结构中。当一个分支被签出时，您的工作目录将会立即反映这些变化。因为 gits 的分布式特性，一个分支可以只存在于您的本地存储库中，而不需要将该分支推到每个存储库中。正因为如此，每当你开发一个新的特性，修复一个 bug 或者其他一些变化时，在 git 中进行分支是很常见的。

[![Branches are just pointers](img/c5ddd78a78dfc3656ddb770fea446192.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/branches-2.png)

在 git 中有一个特殊的指针叫做 HEAD。无论 HEAD 指向什么，都将反映在你的历史和工作目录中。换句话说，头指向的是你所在的分支。

`git branch`

branch 命令列出所有本地分支。使用`--remote`或`-all`只列出远程或本地和远程分支。

`git branch feature`

将从当前分支最新提交创建一个名为 feature 的新分支

`git checkout feature`

这将通过将头指向特征的分支指针来改变特征的分支。

`git checkout -b feature`

`-b`参数将从当前分支指针创建一个新分支，然后切换到新创建的分支。

[![HEAD points to master](img/b5344cd015f8756489c07a527a8d2660.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/HEAD_before_checkout.png)

[![After git checkout feature. HEAD now points to feature](img/d1e3631e29a34cb1a61e45b570a791d8.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/HEAD_after_checkout.png)

checkout 命令也可以用于查看特定的提交。一个用例可能是您想要查看上周是否存在一个 bug。首先获取提交 id，然后使用

`git checkout 318bf`

这会让你处于“超脱头”状态。当您将 HEAD 指向特定提交而不是分支指针时，就会发生这种情况。当在分离的头脑中时，你可以环顾四周并做实验性的改变。

[![Detached HEAD](img/511d1a00dbd4cac5016dcc6b93a8881f.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/Detached_HEAD.png)

但是这也很危险，因为没有指向新提交的指针。请记住，提交只引用一个或多个父级。在正常的分支中，git 有一个分支指针，它总是指向最近一次提交。但是在“分离的头”中，只有头指向最近的提交。当再次使用 git checkout master 返回 master 时，您将不再引用您在“分离头”中所做的新提交。有很多方法可以恢复这些更改，比如使用`git reflog`命令。如果您想在“分离的头”中进行更改或已经进行了更改，只需直接创建一个新分支，通过调用

`git checkout -b my-new-branch`

当使用文件作为参数时，Checkout 还有第三个用例。

`git checkout HEAD -- myfile.txt`

这将用最近一次提交的文件版本覆盖您工作目录中的文件，并且不会更新 HEAD 指向的内容，这意味着您仍然在同一个分支上。“-”参数告诉 checkout 这是一个文件操作。这不是一个必需的参数，但是如果存在与文件同名的分支，它可以防止错误地执行分支操作。

## 合并和这个叫做重定基础的东西

Merge 和 rebase 是两个目标相同的命令，都是将一个分支的变更集成到另一个分支中。最大的区别在于他们是如何做到的。要合并，首先切换到要合并的分支。在我们的例子中，我们在主分支上。那就跑

`git merge feature`

该命令将主合并到特征中，这将创建一个新的合并提交。合并是非破坏性操作，它将来自两个分支的历史与具有两个父分支的新合并提交相结合。

[![The result of merging feature into master](img/49189f7c1435e116343c40374adbab65.png)](//www.mourtada.se/wp-content/uploads/2016/01/merging.png) 
如果主分支非常活跃，并且您必须经常合并，则历史将包含许多合并提交。为了避免这种情况，你可以使用 rebase。

Rebase 改变了你的库的历史，它通过填充你的提交，然后添加来自其他库的提交，最后重放你的提交。

`git rebase master`

将当前分支所基于的基础提交设置为主最新提交。记住 rebase 做什么的一个窍门是在 rebasing for yourself 中发“re”音。

[![Before rebasing master onto feature](img/a9a4b88bd98d9204b7049fbac0d421fc.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/before_rebasing.png)

[![After rebasing master onto feature](img/f942d2afcf3ca7f8d5b3e18b691d6fb8.png)T2】](//www.mourtada.se/wp-content/uploads/2016/01/after_rebasing.png)

在此基础上，你的特征分支从主特征开始。这样做的好处是历史更加清晰，因为它消除了合并提交并创建了一个完美的线性历史。

这里重要的是，你被塞走的提交将获得新的 id:s，这将改变历史。“重定基础的黄金法则”是永远不要重定已经与其他开发人员共享的提交的基础，因为它会改变历史，这可能会将您置于一个麻烦的境地。

## 撤销事情

复位命令用于将磁头复位到特定状态。

`git reset HEAD~1`

这将使 HEAD one 提交向后移动。也可以指定特定的提交 id。这是另一个重要的命令，只在没有与他人共享的更改上执行，因为它更改了历史。Reset 有三个重要参数，它们改变了 reset 的工作方式。

*   - soft -这将把头正在引用的分支指针移动到指定的提交。仅此而已。
*   - mixed -这将把 HEAD 引用的分支指针移动到指定的提交，并更新登台区/索引以反映该提交。**这是默认参数**
*   - hard -这会将 HEAD 引用的分支指针移动到指定的提交，更新登台区/索引并覆盖工作目录以反映该提交。请注意，这将把每个被跟踪的文件重置为该提交。与 git checkout 不同，它不会通知您有未保存的更改，要撤销它，您必须运行“git reflog ”,因为您将有未引用的提交。

当您想要合并提交并对所有更改进行一次提交时，重置也很有用。例如跑步

`git reset --soft <commit>`

将重置历史记录，但保留索引和工作目录不变。那就跑

`git commit -m "my new commit"`

您将清理并合并您以前的提交到一个单独的提交中。

`git reset HEAD <file>`

重置也使重置文件成为可能。如果提供一个文件作为参数，git 用 HEAD 中的文件覆盖 staging 区域中的文件。如果文件不在 HEAD 中，它将对文件进行分段存储。对文件使用 reset 时，git 不会移动分支指针。

revert 命令不同于 reset，因为它将创建一个新的提交，而不包含已还原的更改。这是撤消更改的安全方法，因为历史不会被更改。当分支是公共的时，这应该总是选择。

`git revert HEAD~2`

将恢复第二个最新提交所做的更改，并创建一个新的提交。与重置不同，还原没有文件操作

有时当你提交时，你意识到你忘记了什么或者你提交的信息是错误的。随着

`git commit --amend -m "my new message"`

git 将用一个新的提交替换最近的提交。请记住在提交之前暂存要添加的新文件。

## 使用遥控器

`git remote`

显示您与其他存储库的连接。它可以是您的团队或同事存储库的中央存储库。remote 由一个 url(HTTP/HTTPS 或 SSH)和一个在命令中使用的友好名称组成。使用 git remote，您可以添加、重命名或删除遥控器。

`git fetch <remote> <branch>`

从远程存储库中获取所有提交，并将它们存储在本地。抓取是一种安全的方式，可以查看远程存储库中发生了什么变化，并且不会影响您当前的工作。您可以通过以下方式查看存储库

`git checkout origin/master`

把一个远程分支想象成只读的，当你签出时，它会把你放在分离的头中。要列出所有远程分支，请使用

`git branch -r`

查看远程分支和本地分支之间有什么变化的另一种方法是运行

`git log master..origin/master`

它将向您显示所有发生在 origin/master 中但不在 master 中的提交。如果您改变分支的位置，它将显示母版中的内容，而不是原始/母版中的内容。也可以用以下方式显示文件差异

`git diff master origin/master`

`git pull origin`

是当前分支上获取和合并的简写。Git pull 也可以通过指定`--rebase`来使用重定基而不是合并

`git push origin master`

它会将您的本地更改推送到远程分支。默认情况下，git 只允许在可以进行快速合并时进行推送。快速合并是指自启动以来，远程分支中没有任何更改，而您在本地进行了所有这些更改。要强制更新，用本地分支覆盖远程分支，请使用“- force”。强迫推是不应该经常发生的事情，因为它会改变历史。需要注意的一点是，标签不是自动推送的。

`git push origin master --tags`

将推送您的本地标签。

## 最后...

我希望你喜欢这篇文章，并从中学到一些东西，我知道我肯定有。这篇文章变得比我计划的要长，但是我觉得这是必要的，即使现在，我还是漏掉了一些部分。如果你喜欢这个帖子或者看到一些不正确的地方，请在下面留下评论。