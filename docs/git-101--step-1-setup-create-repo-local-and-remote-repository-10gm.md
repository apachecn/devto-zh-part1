# Git 101 —步骤 1:设置、创建 repo 本地和远程存储库

> 原文：<https://dev.to/mrfrontend/git-101--step-1-setup-create-repo-local-and-remote-repository-10gm>

如果你从未使用过 Git，那就很难开始了。通过这一系列的博客和视频，我想帮助你开始。我们要从终点站开始！如果你不习惯终端(有人称之为命令行)，我尽力向你展示它并不可怕！

让我们从 Git 开始吧🚀🔥

如果你更喜欢阅读而不是看视频？向下滚动，在视频下面，一切都写下来了。

[https://www.youtube.com/embed/hFi0hUnvr0s](https://www.youtube.com/embed/hFi0hUnvr0s)

#### 什么是 Git？

Git 是现在保存你的项目的方法！你自己的小项目或者公司内部大团队的项目。

一个简短的解释是，Git 将跟踪你的项目的所有变化。它将显示谁做了更改，更改了什么以及何时更改的。

如果你愿意，你可以随时回到代码的早期状态。

Git 不仅适合编程项目，也非常适合其他类型的文件。比如设计文件！

#### 安装 Git

首先，我们必须安装 Git 才能使用它。Git 可用于 Windows、Mac 和 Linux。

去 [Git 网站下载](https://git-scm.com)你的操作系统版本。

如果你对安装有任何问题，请在评论中告诉我，或者在 Twitter 或 Instagram DM 上联系我。

#### 设置您的 git 凭证

为了让你的名字和电子邮件与你所做的每一个承诺保持一致。您需要设置 git 名称和 git 电子邮件。

**设置你的 git 用户名**

打开终端并键入 git config-global user . Name“Your Name”来设置您将在机器上使用的 Git 名称。如果删除 global 选项，则该名称将仅在您所在的存储库中设置。

您可以使用 git config user.name 命令检查它。如果你做对了，你的名字就会出现。

**设置你的 git 用户邮箱**

打开终端并键入 git config-global user . email[your@email.com](mailto:your@email.com)来设置您将在机器上使用的 Git 名称。如果删除 global 选项，则该名称将仅在您所在的存储库中设置。

您可以使用 git config user.email 命令检查它。如果你做对了，你的邮件就会出现。

**设置你的 git 密码**

您的密码不能以同样的方式设置。当您将更改推送到远程存储库时，终端会询问您的密码。

但是如果你不喜欢每次推你的改变时都打字，那么就用助手吧。

对于 windows:git config-global credential . helper wincred

对于 Mac:git config-global credential . helper OS xkeychain
T3】对于 Linux:git config-global credential . helper cache

在 Github 文档网站上阅读更多技巧。

#### 启动一个 Git 项目

用 Git 启动一个项目有几种方法。

1.  首先在计算机的本地文件夹中初始化 Git
2.  在 Github、Bitbucket、Gitlab 或任何其他托管 Git 库的公司上创建一个新项目。

#### 在本地创建 Git 项目

让我们从在您计算机的本地文件夹中初始化 Git 开始。

打开终端，输入 git init，然后回车。

有了这个命令，就会有一个隐藏的。git/文件夹。在此文件夹中，将保存您的所有更改。最终，如果您提取这些更改，其他人也会对该存储库进行所有更改。但是 git pull 会在另一集。

#### 在 Github、Bitbucket、Gitlab 或其他地方创建一个 Git 项目

也可以从远程创建存储库开始您的项目。Remote 是在服务器上托管您的 Git 存储库的外部服务/公司。

Github 是大量开源项目中最受欢迎的。Bitbucket 最常用于那些希望保持代码私有的人，或者那些也在使用其他 Atlassian 软件的大公司。Gitlab 是一个较新的服务，但我听到了关于它的好声音。

当然，还有很多，或者你也可以举办你自己的。但是你的代码库应该在一个安全的服务器上！您可以通过键入 git clone 后跟 git-repository-url 来获得整个存储库的副本。

**像这样举个例子:**

git 克隆[https://github.com/raymonschouwenaar/git-demo-project](https://github.com/raymonschouwenaar/git-demo-project)或者如果你想决定文件夹的名称，那么在 url 后面加上文件夹名称。git 克隆[https://github.com/raymonschouwenaar/git-demo-project](https://github.com/raymonschouwenaar/git-demo-project)项目名称

在我的视频中，我向你展示了如何在 Github 上创建一个资源库。所以不知道怎么做就去查一下吧。

#### 需要帮助吗？

我希望这第一集/博客能帮助你设置 Git，并给你一点使用它的介绍。如果你需要任何帮助，请在评论中告诉我！我会❤️来帮你！

*原载于* [*奥前端博客*](https://blog.mrfrontend.org/2017/11/git-101-step-1-setup-create-repo-local-and-remote-repository/) *。*

* * *