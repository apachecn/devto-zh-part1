# Linux 4.12 或更高版本上的 802.11ac 控制器 Realtek 8822BU

> 原文：<https://dev.to/fmtweisszwerg/80211ac-controller-realtek-8822bu-on-linux-412-or-later-e8h>

Edimax EW-7822ULC 使用 Realtek 8822BU 控制器，支持 Linux。但是官方驱动支持内核到 4.11。所以我在 GitHub 上移植了 Linux-4.12，4.13 和 4.14

# 准备中

在构建之前，你必须安装一些软件包。

在 Debian 和 Ubuntu 上，通过 apt 安装软件包:

```
$ sudo apt install dpkg-dev
$ sudo apt install build-essential libc6-dev linux-headers-`dpkg-architecture -qDEB_HOST_ARCH` 
```

如果您喜欢使用 Git 来检索源代码，您必须安装 Git:

```
$ sudo apt install git 
```

# 构建并安装

## 通过走

简单地克隆、检验、构建和安装。

```
$ git clone https://github.com/FomalhautWeisszwerg/rtl8822bu.git
$ cd rtl8822bu
$ make
$ sudo make install 
```

注意，分支“主”意味着“基本上工作”，分支“开发”意味着“不稳定或有错误”。

## 通过 tarball

如果您不想克隆存储库，您可以使用`curl`或`wget`来检索源代码。

```
$ curl -LO https://github.com/FomalhautWeisszwerg/rtl8822bu/archive/release-for-linux-4.14.tar.gz
$ tar -xf release-for-linux-4.14.tar.gz
$ cd rtl8822bu-release-for-linux-4.14/
$ make
$ sudo make install 
```

# 设置

该驱动程序通过使用“nl80211”驱动程序与 wpa_supplicant 配合使用。在“Wicd 网络管理器”上，设置如下截图所示的首选项。

[![](img/d4892ca1e27a661b321067482c10ea61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nPVUta9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrn5lq2v69z43g9p3x80.png)

尽情享受吧！