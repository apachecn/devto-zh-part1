# 如何共享您的 visual studio 代码设置和扩展

> 原文：<https://dev.to/shanalikhan/how-to-share-your-visual-studio-code-settings-and-extensions-39k>

您现在可以与您的用户或团队成员共享您的 visual studio 代码设置，您只需编辑设置或上传您的扩展，它将自动下载到您的用户计算机中。

确保您有 Github 访问令牌。如果没有设置，您需要[配置](http://shanalikhan.github.io/2015/12/15/Visual-Studio-Code-Sync-Settings.html)。

### 创作要诀分享

默认情况下，extension 创建了秘密要点，所以只有你能在 github 页面上看到它。因此，您需要创建公共 GIST，以便任何人都可以看到您的 GIST 内容。

> 按命令:同步:高级选项>与公共要点共享设置

它将删除您当前的要点，并创建新的公共要点和上传设置。您可以与其他用户共享您的 GIST ID，以便他们可以使用您的 gitHub Gist 下载这些设置。

### 从共享 GIST 下载设置

设置同步，默认情况下需要 Github 令牌来下载设置，但是如果你是从 Public Gist 下载的，就不需要这个令牌。您需要打开公共 Gist 下载模式，如下所示:

> 通过命令:同步:高级选项>从公共 Gist 下载设置。

现在当你下载设置时，它将从代码设置文件中的公共文件中下载。

扩展不允许用户编辑或改变其他用户的要点。

欢迎加入我们的频道进行讨论或评论。[![Drawing](img/8f36feaf04c78eb436ab09ec21b9e988.png)T2】](https://join.slack.com/t/codesettingssync/shared_invite/enQtMzE3MjY5NTczNDMwLTYwMTIwNGExOGE2MTJkZWU0OTU5MmI3ZTc4N2JkZjhjMzY1OTk5OGExZjkwMDMzMDU4ZTBlYjk5MGQwZmMyNzk)