# 整理我的桌面

> 原文：<https://dev.to/funkysi1701/tidying-my-desktop-2f1a>

我的桌面总是一团糟。我经常在那里下载文件，然后把它们忘得一干二净。

我不时地将文件复制到子目录中，所以在我的桌面再次失控之前的一两天里，它看起来还算正常。

为什么我不写一个脚本，我可以安排这样做。这样我的桌面就会一直保持整洁。

我已经写了一些简单的批处理脚本，但是当然目前最好的脚本语言是 PowerShell。让我们用那个。

Windows 为编写脚本提供了一个很好的小工具，叫做 Windows PowerShell ISE，所以让我们从加载它开始。

PS 有很多帮助来帮助你，只需运行**Get-Help【PS 命令名称】**

要移动文件，您可以使用 **Move-Item** ，它的工作方式非常类似于复制，指定源和目标。在我的例子中，我根据文件扩展名移动文件。

移动-项目*。pdf 文件夹

现在我需要做的就是安排这个脚本每天运行一次，或者在我每次登录或打开电脑时运行。

PowerShell 可以做更多有趣的事情，希望我很快会在博客上写出来。