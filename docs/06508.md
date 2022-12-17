# 我的 WSL 开发设置

> 原文：<https://dev.to/lloydstubber/my-wsl-setup-for-development>

在 Windows 上运行 Git Bash 遇到一些麻烦之后，我决定为了所有的开发目的迁移到 WSL 上。我对 Linux 非常陌生，所以这是一个非常高层次的概述，介绍了我所做的工作以及我遇到的一些问题。

如果你有什么要添加的，请在下面评论，或者在这里为回购[创建一个 PR。](https://github.com/lloydstubber/My-WSL-Setup)

尽情享受吧！

### 下载&安装 WSL

*   在这里遵循非常详细的说明

### 让你的终端看起来漂亮点 1

*   下载 Hyper.js [这里](https://hyper.is/)
*   我选择了“超蓝”主题

### 在 Bash 中自动打开

*   打开 Hyper，输入`Ctrl` + `,`
*   向下滚动到 shell 并将其更改为`C:\\Windows\\System32\\bash.exe`

### 安装 Zsh

*   运行此`sudo apt-get install zsh`
*   打开您的 bash 档案`nano ~/.bashrc`
*   添加此项以将其设置为默认使用 ZSH:

```
if [ -t 1 ]; then
exec zsh
fi 
```

Enter fullscreen mode Exit fullscreen mode

### 让你的终端看起来漂亮点 2

*   用`sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`安装我的 Zsh
*   阅读文档[这里](https://github.com/robbyrussell/oh-my-zsh)关于如何添加更多插件和改变主题(我用了他们的开箱即用‘robbyrussell’)。

### 确定 ls 和 cd 的颜色

*   开箱即用当你`ls`或`cd` + `Tab`你会在目录上看到一些讨厌的背景颜色。要解决这个问题，请打开您的~/。zshrc 文件，并将其添加到末尾:

```
#Change ls colours
LS_COLORS="ow=01;36;40" && export LS_COLORS

#make cd use the ls colours
zstyle ':completion:*' list-colors "${(@s.:.)LS_COLORS}"
autoload -Uz compinit
compinit 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 Git

*   运行此`sudo apt update`
*   然后运行`sudo apt install git`

### 设置一个 SSH 密钥，链接到你的 Github

*   在这里按照 Linux 步骤[创建一个密钥并将其添加到您的 SSH 代理中](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-linux)
*   然后键入`cat ~/.ssh/id_rsa.pub`
*   从终端复制您的密钥并粘贴到您的 Github 密钥中

### 安装节点版本管理器

*   遵循这里的说明，这将使您在 Node/npm 上运行，并且可以轻松地回滚到合适的版本。

### 安装 Gulp CLI

*   请点击此处查看吞咽文件[。](https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md)

* * *

### 备注

*   在意识到 Ubuntu 不会自动从 14.04 升级到 16.04 之前，我在 Node/npm/Gulp 上遇到了麻烦——正如这篇[文章](https://blogs.msdn.microsoft.com/commandline/2017/04/11/windows-10-creators-update-whats-new-in-bashwsl-windows-console/)中提到的。如果你在同一条船上，通过这些[指令](https://help.ubuntu.com/lts/serverguide/installing-upgrading.html)升级。
*   通过 WSL - inotify 和 socket 问题，我还遇到了旧版本 Windows 10 不喜欢 Gulp 的问题。确保您运行的是 Creators Update 或 Insiders 程序版本。
*   如果你的 shell 运行缓慢(我的是),从 nvm 切换到 [n](https://github.com/mklement0/n-install) ,用 curl 命令安装它。首先确保卸载 nvm 并清除 Nodejs/npm 的任何痕迹。如果 n 在 zsh 上不能作为命令使用，它会把它的路径安装到您的~/中。bashrc 所以把它复制到你的~/.zshrc 就行了。