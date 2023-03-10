# 如何在电脑上正确设置 Git！

> 原文：<https://dev.to/landonpatmore/how-to-properly-set-up-git-on-your-computer-33eo>

# 安装 Git

### OSX 设置

如果您从未在您的计算机上使用过 Git，您很可能不会安装它。打开你的终端，输入`git`。它应该提示你需要安装 **Xcode 命令行工具**来实际安装 Git。下载完成后，Git 将被正确地安装在您的计算机上，您现在可以从命令行使用它了。

### 窗口设置

Windows 没有 Git，也没有像 OSX 一样的内置方式来安装它，而无需安装额外的软件。转到这个链接:[下载 Git](https://git-scm.com/download/win) ，这是一个安装程序下载的链接。浏览这些步骤，保持默认设置，因为没有理由改变它们，除非**你真的知道你在做什么**。这将安装 **Git Bash** ，它是一个 Windows 终端模拟器，允许你访问 Git。

*补充说明:虽然您可以通过命令提示符使用 Git，但是我强烈推荐您使用`Git Bash`,因为与不告诉您任何信息的命令提示符相比，它可以在终端中提供您正在处理的分支的信息，并且您可以使用 Linux 命令。*

一位好心的用户告诉我，你可以为 PowerShell(命令提示符)安装一个插件，这样你就可以像 Git Bash 一样查看分支信息和提交差异！要安装，您必须已经安装了 Git，并在命令提示符下键入以下命令:`Install-Module posh-git -Scope CurrentUser`。如果你因为任何原因对这个插件有疑问，这里有这个库的链接: [dahlbyk/posh-git](https://github.com/dahlbyk/posh-git) 。

### Linux

如果您正在运行 Linux，您很可能知道自己在做什么，并且以前使用过 Git。但是如果你没有，去你的终端，输入`git`。Linux 通常附带 Git，所以它应该已经在那里了，但是如果没有，在终端中键入这个:`sudo apt-get install git`。这将安装 Git，您将能够从那里开始。

# 设置 Git

有几个步骤可以让您正确启动和运行 Git，这样就不会出现任何问题。对于这些步骤，假设您在终端(OSX/Linux)或 Git Bash/命令提示符(Windows)中。步骤如下:

1.  `git config --global user.email "GIT SERVICE EMAIL"` -为 Git 全局设置你的电子邮件。
2.  `git config --global user.name "GIT SERVICE NAME (NOT USERNAME)"` -为 Git 全局设置你的名字。
3.  完成这一步有两种方法:

文章的这一部分假设你正在使用一个叫做 GitHub 的服务。GitHub 是一个非常受欢迎的 Git 服务，它允许你使用他们的服务将你的代码上传到他们的 web 服务器进行版本控制，而不是将所有东西都保存在本地。还有其他服务，如 GitLab、BitBucket 和 Microsoft Visual Studio Git。每种方法都有自己特定的设置后续步骤的方式，但是 SSH 密钥方法将是相同的，直到您必须将密钥添加到您的特定服务中。至于 HTTPS 方法，它可能是完全不同的。每个服务都应该有一个关于这两种方法的文档页面，应该很容易访问。

### 方式 1——生成 SSH 密钥**(推荐)**

这听起来令人望而生畏，但这可能是两种方法中最简单的一种(我使用它)，并且从长远来看更好使用。我们将生成一个 SSH 密钥。来自 Github 的文档:使用 SSH 协议，您可以连接并认证远程服务器和服务。使用 SSH 密钥，您可以连接到 GitHub，而无需在每次访问时提供您的用户名或密码。

步骤:

1.  `ssh-keygen -t rsa -b 4096 -C "GITHUB EMAIL"` -这将创建一个新的 ssh 密钥，使用提供的电子邮件作为标签
2.  当提示您“输入保存密钥的文件”时，请按 Enter 键。这接受默认的文件位置。
3.  在提示符下，键入安全密码。
4.  `eval "$(ssh-agent -s)"` -在后台启动 ssh-agent。
5.  `ssh-add -K ~/.ssh/id_rsa` -将您的 ssh 私钥添加到 SSH 代理中。
6.  将你的 SSH 密钥复制到你的剪贴板。
7.  登录 Github 后，进入右上方，点击您的个人资料图片，然后在下拉列表中点击`Settings`。然后点击**宋承宪和 GPG 键**，或者点击这个链接带你去:[宋承宪和 GPG 键](https://github.com/settings/keys)
8.  点击`New SSH Key`，给你的钥匙起个标题**我的电脑**，把钥匙粘贴在大框里。
9.  完成了。现在，任何时候你克隆一个库，Github 会自动给你一个`SSH` URL，你必须提供你的`SSH Key`释义，任何时候你推和拉改变，你将不再需要输入你的用户名或密码！

### 方式 2 -生成个人令牌

这听起来令人生畏，但也很容易！我们将生成一个`GitHub Personal Token`在我们的计算机上使用。如果我们已经设置了`Two-Factor Authentication`，那么如果我们使用回购协议中的`HTTPS` URL，我们就需要这样做。

步骤:

1.  转到右上角，单击您的个人资料图片，然后在下拉列表中单击`Settings`。
2.  在左下角，点击`Developer Settings`。
3.  在左侧，点击`Personal access tokens`。
4.  点击`Generate New Token`按钮。
5.  在`Token Description`框中，键入令牌的名称，**我的电脑**。
6.  勾选标有`repo`的方框，然后滚动到底部并点击`Generate Token`。
7.  **重要提示**:出于安全原因，你只会看到这个令牌一次，所以如果你愿意的话(我建议你不要用这个方法，所以我不知道你用的时候它是否保存了令牌)，把令牌复制到一个安全的地方。
8.  完成了。现在，任何时候你克隆一个库，Github 都会自动给你一个`HTTPS` URL，你必须提供你的 Github 用户名和密码，这是我们刚刚生成的个人令牌，而不是你实际的 Github 密码，任何时候你推或拉改变(它可能会保存令牌，所以它可能不会每次都问你)。

### 设置全局。gitignore

一位好心的用户向我提到，设置 Git 时另一件非常重要的事情是设置一个全局`.gitignore`文件。Git 使用一个`.gitignore`文件来全局忽略您可能不希望在 repos 中出现的文件列表，例如:。罐子，。exe，。DS_Store 等。要进行设置，请将以下内容复制到您选择的终端:

```
git config --global core.excludesfile '~/.gitignore'
# on a mac:
echo .DS_Store >> ~/.gitignore
# If you use vim
echo '.*.sw[po]' >> ~/.gitignore
# And any other files that should never be checked into git in ANY repo 
```

Enter fullscreen mode Exit fullscreen mode

每个单独的存储库可以有自己唯一的`.gitignore`，但是全局的适用于所有的存储库。

# 仓库设置

**本节适用于所有 Git 服务。**

既然我们已经设置了 Git 客户机，使其像一台运转良好的机器一样工作，现在我们需要对存储库本身进行一些设置。你所在的团队就是你将要参与的回购项目。单击属于您团队的 repo，您将看到您的存储库的概述，其中显示了:文件、提交、协作者和其他内容。

我们感兴趣的是右上角的`Fork`按钮。分叉是将回购复制到自己账户的行为。我们这样做是为了没有人直接在主存储库或者上游的**上工作。**

 **它可能会要求您点击您的用户名或自动开始分叉回购到您的个人资料。几秒钟后，你会看到你的叉子。它看起来完全一样，但现在它是你的个人回购，你可以做任何你想做的事情，它不会影响其他任何人！厉害！我们也知道这是我们的分叉，而不是主回购，因为在左上角，它会告诉你它是从哪里分叉的。

接下来，我们想要克隆我们的**分支**，而不是主回购(你会很快遇到问题！)，点击`Clone or Download`按钮并复制下拉列表中的链接。如果你使用的是`SSH`，不要换成`HTTPS`，反之亦然(再次出现问题！).`cd`放入您希望回购放入的任何目录。接下来输入命令:`git clone`然后粘贴链接后，点击回车。这将把您的 fork 克隆到您的计算机上，这意味着您现在有了您的 fork 的本地副本！现在`cd`进入现在下载的文件夹。

我们几乎完成了设置。我们需要将主回购，或**上游**添加到我们的本地回购中，这样当其他人的**拉请求**被接受时，我们可以拉下更改。这是为了让我们能够始终与主回购保持同步。回到 Github 点击主回购，而不是你的 fork，点击`Clone or Download`按钮，复制你从主回购得到的链接。现在，确保您在您的计算机上的 repo 内部，并键入以下命令:`git remote add upstream`，然后粘贴链接并单击 Enter。它不应该给你任何反馈，这意味着它被添加。要检查它是否被添加，输入`git remote -v`，它将列出遥控器及其 URL。那里应该有`origin`和`upstream`。

我们现在都设置在我们的回购！

# Git 应用

因为有些人可能不太习惯只使用终端进行 Git 操作，所以可以使用几个不同的 Git GUI 应用程序来帮助您可视化正在发生的事情，这样就不会在命令行上搞砸了！

*   源码树(个人用):[链接](https://www.sourcetreeapp.com)
*   GitHub 桌面:[链接](https://desktop.github.com)
*   [链接](https://www.gitkraken.com)

希望这有所帮助！有什么问题，随时问我！

黑客快乐！

*   [姓氏]兰德勒**