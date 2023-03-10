# "我想在 Linux 上使用 Windows 子系统吗？"

> 原文：<https://dev.to/jacoby/do-i-want-to-use-windows-subsystem-for-linux-b39>

## “我要不要在 Linux 上使用 Windows 子系统？”

我不知道。也许吧。

…

是啊，那可能没什么帮助。

## “什么是 Linux 的 Windows 子系统？”

考虑一下，例如 [Debian](https://www.debian.org/) 。

> Debian 项目是一个由个人组成的联盟，他们为了共同的目标创造了一个免费的操作系统。我们创造的这个操作系统叫做 **Debian** 。
> 
> 操作系统是使计算机运行的一组基本程序和实用程序。操作系统的核心是内核。内核是计算机上最基本的程序，它完成所有基本的日常工作，并让你启动其他程序。

我选择 Debian 的原因不是因为它可以在 WSL 中使用，而是因为 Debian 系统在 Linux 内核上使用 GNU 工具。**或**的 [FreeBSD](https://www.freebsd.org/) 内核。

类似地，如果事情做对了，你可以让用户域的东西与 Windows 内核对话。

这不像 [VirtualBox](https://www.virtualbox.org/) ，你有一台完全虚拟化的计算机，在你的计算机中运行一个操作系统，占用你大量的资源。

它不像 [Docker](https://www.docker.com/what-docker) 那样，将容器(我理解不到足以解释，但看做包但更多是)与 chroot 结合在一起。[布莱恩·坎特里尔(Bryan Cantrill)做了一个关于监狱和 Solaris Zones 的演讲，作为 2016 年我们热爱的论文的先驱](http://paperswelove.org/2016/video/bryan-cantrill-jails-and-solaris-zones/)，所以我会链接和手写。

这是一种与 Windows 内核并行的方式，除了通过`msi`和`chocolatey`或`nuget`安装 Windows `PE`二进制文件之外，它还允许你通过`apt`或`yum`安装 Linux `ELF`二进制文件。

这很奇怪。但是有点酷。

## “那么，我需要做些什么来实现它呢？”

首先安装 [Fall Creators 更新](https://support.microsoft.com/en-us/help/4028685/windows-10-get-the-fall-creators-update)。我从 Windows Insider 的“快速通道”开始，它的变化速度有时让人感觉很糟糕。

[然后你需要在 PowerShell 内启用 WSL，重启，然后从 Windows Store 中选择 Ubuntu 或者各种 SUSE。](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

我被告知 Fedora 或 CentOS 来了，但他们不在这里。

如果机器不是你控制的(管理员访问)，你可能不能这样做。

我想指出的是，你还没有视窗系统。一般都是`Kernel -> userland -> pretty pictures`，Windows 已经在垄断`pretty pictures`部分了。可以使用像 [VcXserv](https://sourceforge.net/projects/vcxsrv/) 或者 [Xming](https://sourceforge.net/projects/xming/) 这样的 X 服务器。

我没有安装那些。我觉得有必要重定向 x 已经很多年了。(上一次我这么做是为了帮助调试一个可能是`sshfs`和`fuse`、`samba`、`sublime text`或`gpfs`的问题。结果是`gpfs`在一个我无法控制的大系统上。)如果你能以`C:\Users\jacoby\Development`或`/mnt/c/Users/jacoby/Development`的身份访问你的文件，以及你的 WIMP 工具(Chrome、Firefox、VS Code 等)的制造商。)基本上都是先开发 Windows 和 macOS 再开发 Linux，为什么 X 转发要用二级版本。

我在这里有一个旁白，但我现在不想进入它。稍后提醒我 Unicode。

所以，我认为如果你对你的系统有这种程度的控制，并且希望能够使用你在 Linux 系统上使用的所有酷的东西，WSL 是一个不错的选择。我是个 Ubuntu 迷，所以我会从下面的命令开始。

```
sudo apt-get install build-essential
sudo apt-get update
sudo apt-get dist-upgrade 
```

Enter fullscreen mode Exit fullscreen mode

从那里，它真的取决于你需要什么。虽然还没有全部完成，但是微软正在使用 GitHub 进行 bug 跟踪，所以一定要从这里开始。

此外，博客和社交媒体将帮助您跟踪即将到来的变化和修复。WSL 来自 Windows Insider，这是微软的测试版，所以知道发生了什么是很好的。这里是 Twitter 上的 Windows Insider 和 T2，Rich Turner 是控制台和 WSL 的高级项目经理。

## “那可能比我需要的还要多。”

我也经常这么想。如果你只需要能够`ssh`或`scp`或`rm 2016-10-*.log`或类似的，有两个选择。

[**Git for Windows**](https://git-scm.com/download/win) 或 [**Cygwin**](https://www.cygwin.com/) 。

历史教训: **GNU 项目**启动，代码发布，一切照旧。许多人想使用这些能力，但不知道如何使用，于是**天鹅座**，意为`CYGNUS, Your GNU Support`，就这样形成了。最终，人们希望在 Windows 上使用这些工具，于是他们创建了 Cygwin。这给了你在`PE`中编译的 GNU 工具，并设置为在 Windows 环境中运行。

Git 项目希望用户能够在 Windows 机器上使用`git`，所以他们在 Cygwin 的基础上构建了 Git for Windows。我想。感觉就是这样。

以我的经验，更新 Cygwin 和 Git for Windows 的方式是重装，和 [ActivePerl](https://www.activestate.com/activeperl/downloads) 和 [Strawberry Perl](http://strawberryperl.com/) 都玩不好。(我认为自己是一个 Perl 爱好者，所以这对我来说很重要。)但是，如果路径正确，您可以从 bash 提示符调用 Windows 可执行文件，并且看到我喜欢做类似于`sublime ~/web/lib/*/*-0*.js`的事情，这对我来说至关重要。

## “我在我的 Windows 电脑上不怎么开发

 *如果你的 Windows 机器是你更大的计算世界的“窗口”(是的，双关语)，那么你真的不需要 WSL 的强大功能，甚至不需要 Cygwin。

我要指出的是，PowerShell 是比 CMD 更强大的 Shell(是的，这是双关语),它的行为非常粗鲁。有人告诉我，管道在对象层次上比在字符串层次上工作得更多，这就形成了一个学习曲线。他们想帮你爬上去。

如果你曾经想从 Windows 操作 Linux 电脑，我相信你对 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 很熟悉，它提供了`putty`、`pscp`、`psftp`和`puttygen`。这很好也很奇妙，但是你可能想像在其他地方一样使用`ssh`和`scp`。

**好消息！**他们需要一种方法将包导入 Visual Studio。NET 开发，他们创造了 [Nuget](https://www.nuget.org/) 。但那不是你想要包裹的唯一地方，所以对于普通包裹，还有[巧克力店](https://chocolatey.org/)。

给你一个 SSH 套件，在任何地方都一样。嗯，因为 PowerShell，我无法让`scp *.png linuxbox:.`工作，但是我刚刚开始，所以我确定有办法。

还有很多其他的东西你可以通过`choco`安装。很值得一查。

## “你提到 Unicode？”

是的。是的，我做到了。

我喜欢调整我的提示，给我关于我的环境的信息。这里有一个。

```
🔥 ✔ jacoby@oz 12:46 25°F ☕ ☕ ~/Downloads 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到，有一个度数符号告诉我外面很冷，一个复选标记表示我在 TaskWarrior 中没有即将到期或过期的任务，一个火焰表示我需要重新启动，还有两个咖啡杯。我跟踪我的咖啡，它告诉我今天我喝了两杯。

使用 Linux 和`gnome-terminal`，这显示得很好。

使用 Git bash 使用的终端，我可以获得度数、火焰和勾号，但是咖啡杯不显示。

使用 WSL Ubuntu 和 PowerShell 的终端，无法获得火焰和勾号。

[从 WSL 问题跟踪](https://github.com/Microsoft/WSL/issues/75)中，我看到了来自 [Rich Turner](https://twitter.com/richturn_ms) 的这段话。

> 唉，因为控制台的文本渲染器是基于 GDI 的，我们无法支持像字体回退这样的功能，这种功能允许我们支持包含特定符号集(例如表情符号、克林贡语)的字体，但对于其他字符，我们逐渐回退到更广泛的字体集。我们的目标是在不久的将来用更现代的 DirectWrite 渲染器取代我们的渲染器。
> 当我们做到这一点时，我们将能够用文本做许多非常酷、现代、新奇的事情，而这些事情我们现在根本无法做到。

就像我说的，如果你正在研究这些问题，跟随 Rich Turner 并搜索这些问题。

*但是，*我确实想有一个漂亮的终端来做我在`$PS1`中提到的事情，所以我想 Xming 和 gnome-terminal 可能是实现这个目标的方法。或者可能有更简单的选择。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。*