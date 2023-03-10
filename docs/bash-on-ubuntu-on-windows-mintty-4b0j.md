# Windows + オールインワンmintty 上的 Ubuntu bash

> 原文：<https://dev.to/shiena/bash-on-ubuntu-on-windows-mintty-4b0j>

# 前言

前几天，mattn 告诉你[Bash on Ubuntu on Windows 最好的终端环境，你过来一下。 虽然写了](http://mattn.kaoriya.net/software/bow/20160824234928.htm)的很棒的报道，但原以为[cbwin](https://github.com/xilun/cbwin) 也能一起使用，结果已经合并了！ ！ ！

*   [wsl-终端](https://github.com/goreliu/wsl-terminal)

# 安装

[只需从 wsl-terminal/release](https://github.com/goreliu/wsl-terminal/releases) 下载最新的 7z 或 zip 并展开到任意文件夹中即可。

# 使用方法

*   从 Windows 执行`open-wsl.exe`后，mintty 将启动。
*   运行`tools/add-open-wsl-here-menu.js`可以从资源管理器的上下文菜单中运行 mintty。
*   从 Windows 运行`run-wsl-file.exe`时，可以运行`.sh`等脚本。

## 想作为登录 shell 运行！

保留缺省值不是登录 shell，所以执行路径是启动时的当前目录，但从 mintty 开始使用时，想使用登录 shell。
此时，可以通过以下设定作为登录 shell 运行 bash，从主目录启动。

### v0.5.1 之后的版本

2016/9/9 现在还没有发布，但是通过以下提交在 open-wsl.exe 中添加了`-l`选项，我们将利用它。
[https://github.com/gore Liu/wsl-terminal/commit/1 b1b 74 b 85 f 54 daf b57 c 999 e 46 BF0e 756 a 96 a 96](https://github.com/goreliu/wsl-terminal/commit/1b1b74b85f54dafb57c999e4c46bf0e756a96a96) wsl-terminal/commit/7d0a 8832 ACF 00 f 0678063 C4 fddd 83 ECD 35102 e5e

### v0.5.1 以前的版本

将 wsl-terminal.conf 中的`shell`更改为与上述提交相同的命令。

```
[config]
title="        "
shell=bash -c "cd; bash -l"
use_cbwin=0
use_tmux=0 
```

## cbwin

wsl-terminal 的`bin`文件夹中附有[cbwin](https://github.com/xilun/cbwin) 。
如果从 Bash on Ubuntu on Windows 侧运行安装程序，将`etc/wsl-terminal.conf`的`use_cbwin`设定为`1`，则可以使用 wcmd(Windows 的 cmd )和 wstart(Windows 的 start )

```
cd bin && sudo ./install_cbwin.sh 
```

```
[config]
title="        "
shell=bash
use_cbwin=1
use_tmux=0 
```

至此，在使用 mintty 的同时也可以执行 Windows 命令的环境就完成了。