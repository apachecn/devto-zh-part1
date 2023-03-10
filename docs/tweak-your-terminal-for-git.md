# 为 Git 调整您的终端

> 原文：<https://dev.to/gonedark/tweak-your-terminal-for-git>

为 Git 调整您的终端不仅会使 Git 更容易使用，还会让您的开发伙伴嫉妒。

很多人问我在[get Git 视频系列](https://gettinggit.com)中使用的终端。我会在这篇文章中包括一些我个人的调整。但主要是，我们将添加命令补全并更新 Git 的提示。

这些调整将通过自动完成 Git 命令和引用来节省时间。此外，包含分支名称和状态的提示将提供关于 Git 存储库当前状态的即时反馈。

在开始之前，我们先退一步来谈谈平台。

如果您在 Windows 上，命令完成和 Git 提示符都包含在 [Git Bash](https://git-for-windows.github.io) 中。我推荐使用它，因为它不仅模拟了一个通用的 shell，还包含了最新版本的 Git。

如果您使用的是基于 Unix 的系统，包括 macOS，那么您的系统应该包含一个运行默认 shell 的终端应用程序。例如，我现在使用的 macOS 默认运行 *bash* shell。而其他的炮弹也有，比如 *zsh* 或者 *csh* ， *bash* 就是我用的。

不管您使用什么 shell，更新提示符和添加命令补全都有类似的设置。也就是说，这篇文章中的例子是针对 *bash* 和 macOS 的。

## Git 命令完成

首先，让我们添加命令完成。命令补全将允许我们键入 Git 命令或引用的一部分，比如分支名称，然后按下`Tab`来自动补全其余部分。

在 Git 开源项目的贡献中有用于命令完成的脚本。让我们在 GitHub 上访问 Git 的[库。我将为 *bash* 打开一个，但是还有更多脚本可用于其他 shells。](https://github.com/git/git/tree/master/contrib/completion)

脚本注释包括安装命令完成的步骤。让我们遵循这些步骤。

首先，我将查看 *raw* 版本，这样更容易复制文件的内容。然后我会回到终点站。

而我可以创建一个新文件，粘贴内容，并保存文件。我可以在 macOS 上使用`pbpaste`命令将我复制的内容输出到一个名为`git-completion.bash`的文件中，一步就可以完成。例如:

```
pbpaste > git-completion.bash 
```

Enter fullscreen mode Exit fullscreen mode

下一步是在我们的 shell 启动时包含这个文件。

为此，注释建议在`.bashrc`文件中添加下面一行。

但是，macOS 默认不使用这个文件。所以取而代之，我把它加到`.bash_profile`里。没有这个文件也不用担心。你可以创造一个。

让我们添加下面一行来确保`git-completion.bash`文件存在，如果存在，就包含该文件。

```
[[ -f "$HOME/git_completion.bash" ]] && source "$HOME/git_completion.bash" 
```

Enter fullscreen mode Exit fullscreen mode

保存后，我们可以启动一个新的终端以使这些更改生效。

现在，您可以测试命令完成情况。键入`git chec`、`Tab`，它会自动完成`git checkout`。

如果某些内容不能自动完成，再次按下`Tab`将显示所有可能的选项。

例如，如果我输入`git ch`，`Tab`，它会自动完成尽可能多的命令。再次按下`Tab`会显示从`che`开始的所有可能的命令。

在这种情况下，`checkout`、`cherry`和`cherry-pick`。

## 更新 Git 提示

接下来，让我们更新我们的提示。

如果我们回到 GitHub 上的脚本，我们也会看到`git-prompt.sh`。这个脚本兼容 *bash* 和 *zsh* 。

与之前类似，我们将复制文件的*原始*内容，并将它们粘贴到一个`git-prompt.sh`文件中。

接下来，我们还将把这个文件包含在`.bash_profile`中:

```
[[ -f "$HOME/.git_prompt.sh" ]] && source "$HOME/.git_prompt.sh" 
```

Enter fullscreen mode Exit fullscreen mode

还有一步。这个文件提供了 Git 提示符的代码，它不配置您的提示符。

为此，我们还将添加以下代码行:

```
PROMPT_COMMAND='__git_ps1 "\u@\h:\w" "\\\$ "' 
```

Enter fullscreen mode Exit fullscreen mode

保存后，我们可以再次启动新的终端以使这些更改生效。

您可以导航到 Git 存储库来查看新的提示。

## 附加调整

现在，这个提示与我在*获取 Git* 视频系列中使用的提示不匹配。在介绍我的个性化之前，我想先介绍一下默认设置。

到目前为止，我介绍的所有内容都应该与运行流行 shell 的任何基于 Unix 的终端兼容，例如 *bash* 或 *zsh* 。

首先，虽然 macOS 附带了一个终端应用程序，但 iTerm2 是一个受欢迎的替代品。我发现它有一个更简洁的用户界面，更易于配置。

此外，iTerm2 还有几十种[颜色主题](http://iterm2colorschemes.com)可供选择。例如，我用的是稍微修改过的*明晚八十年代*主题。

最后，我定制了我的命令提示符。您可以从 [my dotfiles](https://github.com/jasonmccreary/dotfiles) 中复制它以及其他 shell 配置文件

在本例中，我定制了提示符，以包含最后一个命令的返回状态，并简化了一些输出。

我更新了我的`.bash_profile`,将上面添加的`PROMPT_COMMAND`行替换为:

```
[[ -f "$HOME/.bash_prompt" ]] && source "$HOME/.bash_prompt" 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我打开一个新的终端，我会看到一个与我们在*获取 Git* 视频中看到的相匹配的提示。

***想学习更多 Git？**在我的视频系列[获取 Git](https://gettinggit.com) 中，获得超过 60 个关于 Git 命令和日常场景的视频。*