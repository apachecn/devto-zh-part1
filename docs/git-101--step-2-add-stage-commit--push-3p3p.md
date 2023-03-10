# Git 101 —步骤 2:添加、暂存、提交和推送

> 原文：<https://dev.to/mrfrontend/git-101--step-2-add-stage-commit--push-3p3p>

[![](img/601443e33f2a0a9bcaf54042b49c0036.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EJ7wXG-S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJiOAw0CRVzWMt-IKBRw7gw.png)

在之前的文章中，我介绍了 Git T1 以及如何安装它。在这个视频中，我将向你展示如何管理你的变更，提交它们，并最终将它们推送到 Github。

不要再等了！让我们开始吧！🔥

[https://www.youtube.com/embed/t-V1CYaPI1M](https://www.youtube.com/embed/t-V1CYaPI1M)

为了在 Github 上获得您的文件，我们需要向本地 Git 存储库添加一个远程。

#### 添加遥控器

您可以通过此命令添加遥控器。

```
git remote add origin http://yourGithubURL 
```

在本例中，is origin 是遥控器的名称。但我们可以随意命名。

甚至有可能有多个遥控器。给它取个别的名字就行了，比如 github 或者 secondremote。

#### 自述文件

每个存储库都需要有适当的 README.md 文件。因此您可以包含安装项目的所有细节。

文件扩展名“md”代表降价。Markdown 是一种以非常简单的方式编写和添加样式的极简方式。

Github 创建了一个[指南来告诉你关于 Markdown](https://guides.github.com/features/mastering-markdown/) 的一切以及如何使用它。

#### 检查局部变化

例如，我们对我们的 [Git 库](https://github.com/raymonschouwenaar/git-demo-project)做了一些更改。但是如果我们想看到所有的变化，我们必须向 Git 请求状态。你可以通过这个命令来完成。

```
git status 
```

然后 git 将向您显示被更改、删除或创建的文件。还包括已转移或尚未转移的文件。

这个例子来自另一个项目，但是向你展示了它的样子！

[![Git status result example](img/ebb7474bf8eedb5e3aea4ed81bda799f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ry1HupGA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AmCdgRLjs9vGLsaG2.png) 

<figcaption>*这是一个`git status`将返回的结果的例子。*</figcaption>

#### 阶段文件

但是在做出更改并检查它之后，我们必须准备更改，所以我们可以将它绑定到提交。

```
git add . 
```

正在暂存所有文件。

如果我们想做一个特定的文件，我们需要使用这个命令。

```
git add foldername/filename.js 
```

#### 提交文件

不，我们终于可以在文件中添加提交消息了。为此，我们使用:

```
git commit -m "This is our commit message" 
```

现在，我们所有的阶段性变更都绑定到这个提交。

#### 将文件推送到远程主机

执行提交后，您可以将该提交推送到远程存储库。

我会推荐它！但是有可能继续进行更改和更多的提交，并最终推动一切。但是多做几次更安全。

要将您的更改推送到远程存储库，我们必须使用这个命令。

```
git push origin master 
```

在这种情况下,“origin”是我们的遥控器的名称,“master”是我们正在工作的默认分支。下一次我将向你展示更多关于树枝的知识。

所以现在我们所有的更改在 Github 中都是安全的👍

#### 需要帮助吗？

我希望这第二集/博客有助于您对 Github(或任何其他远程存储库)进行更改。如果你需要任何帮助，请在评论中告诉我！我会❤️来帮你！

*原载于* [*奥前端博客*](https://blog.mrfrontend.org/2017/12/git-101-step-2-add-stage-commit-push/) *。*

* * *