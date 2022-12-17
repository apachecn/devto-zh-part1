# 在 Windowsからcmd / startを的 Ubuntu 上狂欢使う

> 原文：<https://dev.to/shiena/bash-on-ubuntu-on-windows-cmd-start-1pj9>

# 前言

Bash on Ubuntu on Windows 可以访问 Windows 文件，反之亦然。
但是，因为是其他系统，所以不能从 Bash 运行 Windows 的 exe 文件，也不能用与
扩展名关联的程序打开文件。

因为有了能做点什么的[cbwin](https://github.com/xilun/cbwin) ，所以试着导入了一下。

# 安装 cbwin

1.  在 Bash on Ubuntu on Windows 端，从[cbwin/releases](https://github.com/xilun/cbwin/releases) 下载 zip 并展开。
2.  展开后执行`install.sh`。
3.  将附带的`outbash.exe`复制到 Windows 端的任意路径。

```
$  curl -LO https://github.com/xilun/cbwin/releases/download/v0.9/cbwin-bin-0.9.zip
$  unzip cbwin-bin-0.9.zip
$  cd cbwin-bin-0.9
$  sudo ./install.sh
$  mkdir /mnt/c/bin
$  cp outbash.exe /mnt/c/bin 
```

至此，`/usr/local/bin`中安装了`wcmd`、`wstart`、`wrun`。
分别是代替`cmd`、`start`而直接执行`CreateProcess`的命令。

# 执行 cbwin

运行 outbash.exe 而不是运行 Bash on Ubuntu on Windows 的 bash。
这样看起来不变，但是可以使用安装的 3 个命令。