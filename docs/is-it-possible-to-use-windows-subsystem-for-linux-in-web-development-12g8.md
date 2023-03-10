# web 开发中有没有可能使用 Windows 子系统 for Linux？

> 原文：<https://dev.to/mmyoji/is-it-possible-to-use-windows-subsystem-for-linux-in-web-development-12g8>

我买了一台用于游戏的 Windows 10 家用电脑(PUBG)，并在 web 开发中尝试了用于 Linux 的 Windows 子系统(又称 Bash on Ubuntu on Windows)。

## 结论

你可以使用 WSL 进行 *light* web 开发。

举个例子，

*   web 前端开发(使用 webpack)
*   小型网络应用程序(使用 Sinatra)
*   等等。

它不适合繁重或现代化的开发环境。

至少，Windows10 家庭版无法轻松使用 Docker。

我从来没有试过 Windows10 Pro + Docker for Windows，我读了一些关于它的文章，它似乎并不容易。

## 语言

我安装并尝试了以下语言:

*   节点. js
*   红宝石
*   Golang

## 为 Linux 设置 Windows 子系统

一点都不难！我跳过指令。

日本人

> [试着安装 Windows Subsystem for Linux 吧！ - Qiita](https://qiita.com/Aruneko/items/c79810b0b015bebf30bb)

英语

> [在 Windows 10 |微软文档上安装 Linux 子系统](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

## 编辑- VSCode

我在 Xubuntu 上的当前主开发环境中使用 NeoVim。

但我此时舍弃了 NeoVim，选择了 VSCode。

它很容易安装和配置，我想用一点。

您只需要关心终端和 git 路径设置。

> [在 Visual Studio Code 中利用 WSL 的 git 进行源代码管理- Qiita](https://qiita.com/xerespm/items/ed4d659cfac4a1695f4b)

从[这里](https://github.com/andy-5/wslgit/releases)安装最新版本的`wslgit.exe`，放在你喜欢的任何地方。(我把它放在`/mnt/c/Users/mmyoji/`下面)

VSCode 设置文件如下:

```
{  "terminal.integrated.shell.windows":  "C:\\WINDOWS\\System32\\bash.exe",  "git.path":  "C:\\Users\\<username>\\wslgit.exe"  } 
```

Enter fullscreen mode Exit fullscreen mode

像`mmyoji`一样替换自己名字的`<username>`。

安装你喜欢的插件。

然而，当您在 WSL 下安装 Gplang 时，Golang 与 VSCode 的集成不起作用。如果你想把 VSCode 和 Golang 一起使用，安装到 Windows 本身可能更好。

## 设置节点

apt 的 node 和 npm 版本太旧，安装过程有点麻烦。

> [在 Ubuntu 上顺利安装最新的 node.js-qi ita](https://qiita.com/seibe/items/36cef7df85fe2cefa3ea)

```
$ sudo apt install -y nodejs-legacy npm
$ sudo npm i -g n

# Install latest node
$ sudo n stable
$ sudo ln -sf /usr/local/bin/node /usr/bin/node

$ node -v
v9.2.1
$ npm -v
5.5.1

$ sudo apt purge -y nodejs-legacy npm 
```

Enter fullscreen mode Exit fullscreen mode

## 设置终端- Hyper

我无法在命令提示符或 PowerShell 下保持冷静，并使用了 [Hyper](https://hyper.is/) 。

*   从官方网站获取`Hyper`。
*   如果你想安装插件。

在我的环境中，配置文件位于`/mnt/c/Users/mmyoji`下。

这是一个例子:

```
// .hyper.js
{
  // ....
  plugins: [
    'hyper-dracula' // theme plugin
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

从`hyper`选项中选择`View` - > `Full Reload`。

它很容易定制，我喜欢它。

## 设置 Ruby 环境

*   使用`git`执行正常的 rbenv / ruby-build 设置指令
*   安装`$HOME/.rbenv`并在`$HOME/.bashrc`(不是`$HOME/.bash_profile`)中添加一些脚本
*   一件事:
    *   在运行`rbenv install 2.5.0`之前，你必须安装`readline`
    *   `$ sudo apt install -y libreadline-dev`

## Setup Golang

可以做和 Linux 一样的设置操作。

但是不太支持 WSL+VSCode 集成。

## 设置 Docker

如果您必须处理 dockerized 应用程序，您必须使用:

*   Windows 10 专业版和 Docker for Windows(以及一些定制版)
*   坞站工具箱(VirtualBox)
*   马科斯
*   Linux 操作系统

可能有一个好的解决办法，我现在找不到。

## 再次得出结论

使用 Windows10 Home 进行现代 web 开发有点困难。

Linux 更适合它。

但是你可以用 Windows10 Home 作为我上面说的 **light** dev 环境。

谢了。