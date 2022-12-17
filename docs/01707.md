# 你能用 Powershell(开发者版)⚔做什么💝

> 原文：<https://dev.to/itsjzt/what-can-you-do-with-powershell-for-developers--edh>

[![HERO](img/bf63469a38c8e8c6f9867537931b8906.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ruFOGv0k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.makeuseof.com/wp-content/uploads/2014/12/basic-powershell-commands-intro-670x335.png)

通常，开发人员会谈论使用命令行工具可以提高工作效率，但可悲的是，他们总是谈论 iTerm 或 BASH 可能是 ZSH，但 windows 用户呢？我知道 windows 中的命令行很糟糕，但仍然有一些希望，这里我们有 Powershell。Powershell 拥有像 curl 和 ls 这样的大部分命令(它们以 windows 风格工作)。

我决定看看我能用 Powershell 走多远，这就是我想出的办法。

## PowerShell 是可定制的🤓

您可以用不同的颜色、许多输出来改变 PowerShell 的提示符，并且像所有 Bash 孩子一样，您还可以用 Post-git 之类的模块来查看 git 状态。如果你想更进一步，甚至改变提示的标题。

就像 BASH 的人将他们的配置保存在`~/.bashrc`中一样，我们可以将我们的配置保存在`~\Documents\WindowsPowerShell\profile.ps1`中

你可以用`Set-PSReadlineOption -BellStyle None`移除铃声或者导入类似`Import-Module posh-git`的模块

如果你不喜欢这个默认提示，你也可以自定义它。

下面是我如何定制我的提示