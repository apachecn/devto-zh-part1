# 在 windows 上安装新 Git

> 原文：<https://dev.to/shiena/bash-on-ubuntu-on-windows-git-b0j>

# 前言

在 Bash on Ubuntu on Windows 上愉快地安装了 apt-get install git，在 1.9.1 版中有点悲伤。
好不容易才能轻松安装新版本，试着找一下没什么大不了的，虽然名字很夸张，但是从用户的角度来看是 Ubuntu 14.04.4 LTS，所以使用 ppa 就可以了。

# 安装新的 Git

```
$ sudo add-apt-repository ppa:git-core/ppa
$ sudo apt-get update
$ sudo apt-get install git 
```

只有这样
2016-08-17 现在可以安装 2.9.3 版了。

# 参考链接

*   [如何在 Ubuntu 上安装 Git 2 . 8 . 1&Linux mint](http://tecadmin.net/install-git-on-ubuntu/)