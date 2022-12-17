# 自动管理个人和工作 git 配置

> 原文：<https://dev.to/fedekau/automatically-managing-personal-and-work-git-configurations-27ad>

*最初发表于 [WyeWorks 博客](https://wyeworks.com/blog/2018/1/15/automatically-managing-personal-and-work-git-configurations/)。*

有一段时间，我发现自己经常在个人电脑和工作电脑之间切换，真的很烦。来回切换了一段时间后，我决定只用一台电脑。

在决定采用单台计算机的方法后，最让我恼火的是，当从工作存储库转移到个人存储库时，必须记住切换我的 Git 用户名、电子邮件和 SSH 密钥，反之亦然。我会经常忘记这样做，而且，时不时地，我会发现工作提交用我的个人电子邮件标记，或者反过来。

在工作中，大多数存储库是私有的，但在家里，我的大部分工作都是公开的，这意味着我的工作电子邮件变成了公开的，我不喜欢这样。因此，在这篇文章中，我将分享一种方法，你可以忘记来回切换，让计算机自动为你做这件事。

## 步骤 1:配置您的 SSH 密钥

如果您正在利用 SSH 与您的 Git 服务器(可能是 GitHub)进行交互，请继续，亲爱的读者。否则，跳过这一步。(或者，更好的是:**开始使用 SSH！**)

假设您有两个 SSH 密钥对，一个用于`user@work.com`，一个用于`user@personal.com`。在推送、克隆或者以其他方式分别与工作或个人存储库进行交互时，您会希望使用相应的 SSH 密钥对。这很容易实现——只需创建或修改一个现有的`~/.ssh/config`文件，如下所示:

```
#personal
Host github.com-personal
  HostName github.com
  User personal
  IdentityFile ~/.ssh/id_rsa_personal
  PreferredAuthentications publickey

#work
Host github.com-work
  HostName github.com
  User work
  IdentityFile ~/.ssh/id_rsa_work
  PreferredAuthentications publickey 
```

Enter fullscreen mode Exit fullscreen mode

将`personal`替换为您的个人 Git 用户名，将`work`替换为您的工作相关用户名，这一步就完成了。

*编辑*

克隆您的存储库时，您应该使用您的自定义主机配置，如果您正常克隆`git clone git@github.com:personal/repo.git`，现在您应该使用`git clone git@github.com-personal:personal/repo.git`。如果你想在现有的仓库中使用它，只需更新遥控器。

## 第 2 步:将用户详细信息添加到工作和个人文件夹

对于这一步，我假设你所有的工作资料都在一个`~/work`文件夹中，所有的个人资料都在一个`~/personal`文件夹中。如果没有，那么你要么在你的电脑上移动一些东西，要么根据你的个人需要调整这一步。

在每个文件夹中，添加一个名为`.gitconfig`的文件。您将最终得到一个`~/work/.gitconfig`文件和一个`~/personal/.gitconfig`文件。在每个文件中，您应该相应地填写您的信息。例如:

```
[user]
  email = user@work.com
  name = John Doe 
```

Enter fullscreen mode Exit fullscreen mode

```
[user]
  email = user@personal.com
  name = John Doe 
```

Enter fullscreen mode Exit fullscreen mode

第二步，完成！现在让我们进入最后一步，把所有的东西放在一起。

## 最后一步:基于当前文件夹覆盖 git 配置

在这一步，也是我们的最后一步，我们将告诉 Git 根据您正在处理的内容使用不同的配置。首先，您需要创建或修改`~/.gitconfig`文件(如果已经存在的话)并将它修改成如下所示:

```
[includeIf "gitdir:~/work/"]
    path = ~/work/.gitconfig

[includeIf "gitdir:~/personal/"]
    path = ~/personal/.gitconfig 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 Git 当你在`~/work`下的任何文件夹时加载`~/work/.gitconfig`文件，当你在`~/personal`下时加载另一个配置。

## 结论

现在您已经配置了 Git，它将根据您正在处理的内容自动选择要使用的用户、电子邮件和密钥对。你可以忘记不小心搞混了用户名，你也不必再担心在工作中泄露你的私人邮件，或者更糟糕的是，向公众泄露你的工作邮件。

您可以进一步定制每个文件的配置，并最终为每个环境定制 Git。例如，您可以配置您的存储库来执行一些钩子，正如我在我的上一篇文章[中解释的，使用 git 钩子来改进您的日常工作流程](https://wyeworks.com/blog/2018/1/3/using-git-hooks-to-improve-your-day-to-day-workflow/)。

希望这一信息证明是有用的。无论如何，请留言告诉我吧！