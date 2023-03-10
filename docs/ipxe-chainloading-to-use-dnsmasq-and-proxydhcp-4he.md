# iPXE 链加载使用 dnsmasq 和 proxydhcp

> 原文：<https://dev.to/arachan/ipxe-chainloading-to-use-dnsmasq-and-proxydhcp-4he>

我想试试链式装载。tftp 速度很慢，传输的数据很少。http 速度快，能够传输更多的数据。
这是用下划线标出的数字。

```
LAN Boot > dhcp-boot > undionly.kpxe from tftpd > boot_menu.ipxe from local httpd > global httpd boot 
```

Enter fullscreen mode Exit fullscreen mode

## 环境

我不是网络管理员。我无法更改 DHCP 服务器设置。
所以，我用代理 DHCP。

> proxyDHCP 服务器仅向 PXE 标识的引导客户端提供 TFTP 服务器 IP 地址和 NBP 名称。 <sup id="fnref1">[1](#fn1)</sup>

使用 proxyDHCP 主要软件是 Dnsmasq。Dnsmasq 提供网络基础设施。
Dnsmasq 也提供网络引导和代理 dhcp。
我只用网络引导和代理 dhcp。
Dnsmasq 可以安装使用 yum 或者 apt-get。

*   计算机网络服务器
    *   Debian Wheezy(Linux pxeserver 3 . 2 . 0-4-amd64 # 1 SMP Debian 3 . 2 . 78-1 x86 _ 64 GNU/Linux)
    *   ECS 945GCT-M
    *   英特尔奔腾双 CPU E2160 @1.80GHz
    *   Dnsmasq 2.62-3+deb7u3
    *   代理 DHCP
    *   iPXE (undionly.kpxe)
*   客户端(client)
    *   台式电脑
    *   ECS G45T-M5
    *   realtek pcie gbe 系列控制器
    *   Intel UNDI,PXE-2.0 (bulid 082)
    *   realtek RTL 8139(A/B/C)/RTL 8130 PCI 快速以太网控制器 v2.11
*   网络
    *   172.20.0.0/16
    *   十亿瓦特(gigawatt)
    *   172.20.0.254
    *   DHCP 服务器
    *   172.20.0.70

## 设置

1.  dnsmasq 安装
2.  dnsmasq 配置设置
3.  从 git 获取 iPXE 源
4.  iPXE 构建到 undionly.kpxe
5.  部署 undionly.kpxe
6.  web 启动 ipxe 菜单
7.  让我们试试靴子。

### Dnsmasq

首先，
我安装 dnsmasq。
我经常用 debian 和 ubuntu。
CentOS/RedHat 的 dnsmasq 是相同的包名和相同的配置文件目的地。

#### 安装

Debian/Ubuntu

```
$ sudo apt-get install dnsmasq 
```

Enter fullscreen mode Exit fullscreen mode

红帽/厘斯

```
$ sudo yum install dnsmasq 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置

Debian/Ubuntu/CentOS/Redhat

```
$ cat /etc/dnsmasq.conf | grep -v -e ^# -e ^$
dhcp-range=172.20.0.0,proxy
dhcp-boot=tag:!ipxe,undionly.kpxe,172.20.0.225
dhcp-match=set:ipxe,175 # gPXE/iPXE sends a 175 option.
dhcp-boot=tag:!ipxe,undionly.kpxe
dhcp-boot=http://172.20.0.225/a.ipxe,172.20.0.225,172.20.0.225
pxe-service=tag:!ipxe,x86PC,"splash",undionly.kpxe
enable-tftp
tftp-root=/var/ftpd
log-queries
conf-dir=/etc/dnsmasq.d 
```

Enter fullscreen mode Exit fullscreen mode

### iPXE

从 [undionly.kpxe](http://boot.ipxe.org/undionly.kpxe) 下载很容易。
但我想在一年前建立源代码。
undi duly . kpxe 从源代码构建显示父代码，
链式加载时。
我想检查父哈希代码。我想从父哈希代码中检查修复日期。
因此，我从 [git 库](http://git.ipxe.org/ipxe.git)中获取源代码并构建它。

#### 获取来源

我需要安装 git。
我安装 git-core，如下所述

```
$ sudo apt-get install git-core 
```

Enter fullscreen mode Exit fullscreen mode

我需要下划线包来构建 iPXE。

*   （同 groundcontrolcenter）地面控制中心
*   binutils
*   制造
*   实际抽取与汇报语言
*   syslinux
*   liblzma
*   xz

我安装下划线。

```
$ sudo apt-get install gcc,binutils,make,perl
$ sudo apt-get install syslinux,lzma-dev,liblzma5 
```

Enter fullscreen mode Exit fullscreen mode

我从 git 库获取 ipxe 源代码到我的当前目录。

我将 git 作为下划线执行。

```
$ cd ~
$ git clone git://git.ipxe.org/ipxe.git 
```

Enter fullscreen mode Exit fullscreen mode

#### 打造

它是当前目录中的 ipxe 目录。
我以下划线的形式进入 ipxe 目录，构建 undi duly . kpxe .

```
$ cd ~/ipxe/source
$ make bin/undionly.kpxe 
```

Enter fullscreen mode Exit fullscreen mode

## 部署

```
$ cd ~/ipxe/source
$ mv bin/undionly.kpxe /var/ftpd 
```

Enter fullscreen mode Exit fullscreen mode

## httpd

我准备 apache2。

你可以准备你喜欢的 httpd，nginx，perl 或者 python 的简单的一行 web 服务器...等等。

### 安装

```
$ sudo apt-get install apache2 
```

Enter fullscreen mode Exit fullscreen mode

### 文档根

Debian 和 Ubuntu 的 apache2 是默认的虚拟主机。
默认的 DocumentRoot 是/var/www。

检查虚拟主机

```
$ cat /etc/apache2/apache2.conf | grep sites-enabled
#       `-- sites-enabled
# * Configuration files in the mods-enabled/ and sites-enabled/ directories
Include sites-enabled/ 
```

Enter fullscreen mode Exit fullscreen mode

Check DocumentRoot

```
$ cat /etc/apache2/sites-enabled/000-default | grep DocumentRoot
        DocumentRoot /var/www 
```

Enter fullscreen mode Exit fullscreen mode

### 开机菜单

我移动 web deploy 文件夹，/var/www/。

我让 boot.ipxe 使用编辑器。

请显示下划线。

```
$ sudo cd /var/www
$ sudo vi boot.ipxe
#!ipxe
menu install menu
item ubuntu Ubuntu installation
item centos7 Centos7 installation
item --gap
item back install menu
choose --timeout 20000 --default back target && goto ${target} || goto menu

:ubuntu
set ubuntu http://archive.ubuntu.com/ubuntu/dists/trusty/main/installer-amd64/curreimg/netboot/ubuntu-installer/amd64
initrd ${ubuntu}/initrd.gz
kernel ${ubuntu}/linux tasks=standard vga=788 -- quiet
boot

:centos7
set centos7 http://mirror.centos.org/centos/7/os/x86_64
initrd ${centos7}/image/pxeboot/initrd.img
kernel ${centos7}/image/pxeboot/vmlinuz vga=788 repo=${centos7}
boot

:back
exit 
```

Enter fullscreen mode Exit fullscreen mode

并保存 boot.ipxe。

## PXE 开机

准备好了！

我打开客户端电脑。首先，BIOS 出现了。
显示下划线。
我想显示 BBS(BIOS 启动画面)。
所以，很快我在电脑开机时按下 F11 键。

```
American
Megatrends

AMIBIOS(C) 2008 American Megatrends. Inc.
G43T-M5 BIOS Release 09/28/2009 for MCJ
CPU : Intel(R) Celeron(R) CPU E3300 @ 2.5GHz

Press DEL to run Setup
Press F11 for BBS POPUP
The MCH is operation with DDR2 800
DRAM Timing: Tcl:6/Tras:18/Trcd:6/Twr:6/Trfc:52/Twtr:3/Trrd:3/Trtp:3
Single Chanel Mode
2048MB OK (64MB Shared Memory SIze Used)
Auto-Detecting Pri Slave...IDE Hard Disk
Auto-Detecting 3rd Master..ATAPI CDROM
Pri Slave : SAMSUNG HD252HJ 1AC01118
            Ultra DMA Mode-5, S.M.A.R.T. Capable and Status OK
3rd Master: HL-DT-ST DVDDAM GH24NS50 XP01
            Ultra DMA Mode-5 
```

Enter fullscreen mode Exit fullscreen mode

客户端显示 BIOS 启动屏幕。

我选择 Realtec 启动代理并按回车键。

```
Press select boot device:
--------------------------------
   SAMSUNG HD252HJ
   HL-DT-ST DVDRAM GH24NS50
   Genetic USB SD Reader
   Genetic USB CF Reader
   Genetic USB SM Reader
   Genetic USB MS Reader
-> Realtec Boot Agent

--------------------------------
^ and ^ to menu selection
ENTER to select boot device
ESC to boot using defaults 
```

Enter fullscreen mode Exit fullscreen mode

客户端更改显示。

```
Realtec RTL8111B/8111C Gigabit Boot Agent
Press Shift-F10 to configure .......... 
```

Enter fullscreen mode Exit fullscreen mode

启动 PXE 开机关闭。

```
Intel UNDI. PXE-2.1 (build 002)
Copyright (C) 1997-2000 Intel Corporation

For RealTek RTL8111B/8111C Gigabyte Ethernet Controller v2.14(000225)

CLIENT MAC ADDR: 00 25 11 CB 41 1B GUID 00020003-0004-0005-0006-00700080009
CLIENT IP: 172.20.10.83 MASK 255.255.0.0
DHCP IP: 172.20.0.70 PROXY IP:172.20.0.225
GATEWAY IP: 172.20.0.254

Auto-Select:
     splash

BOOT SERVER IP : 172.20.0.225

PXE-EB: !PXE at 9C3E:0070,entry point at 9C3E:0109
             UNDI code segment 9C3E:1A7A, data segment 923D:A010 (584-632kB)
             UNID device is PCI 02:00.0, type DIX+802.3
             584kB free base memory after PXE unload
iPXE initialising devices...ok

iPXE 1.0.0+ (55e4) -- Open Source Network Boot Firmware -- http://ipxe.org
Features: DNS HTTP iSCSI TFTP AoE ELF MBOOT PXE bzImage Menu PXEXT

Press Ctrl+B for the iPXE command line... 
```

Enter fullscreen mode Exit fullscreen mode

从 Web 服务器启动菜单。

选择 Ubuntu 安装或 CentOS7 安装，然后按回车键。

从互联网资源启动 Linux 安装程序。

```
 Install Menu

Ubuntu installation
CentOS7 installation 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html)
    *   PXE 和 proxyDHCP 软件包
*   [iPXE 链式装载](http://ipxe.org/howto/chainloading)
    *   如何使用 iPXE 链加载
*   [iPXE 下载](http://ipxe.org/download)
    *   如何在源代码部分从源代码构建 iPXE？
*   [预启动 _ 执行 _ 环境](https://en.wikipedia.org/wiki/Preboot_Execution_Environment)
    *   解释预启动执行环境(PXE)
    *   集成部分为我们解释了 proxydhcp。
*   [如何在 Debian 8 上安装 Git](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-debian-8)
    *   从 git 获取 ipxe 源代码
*   [iPXE -多功能引导加载程序](http://blog.schlomo.schapiro.org/2014/07/ipxe-versatile-boot-loader.html)
    *   简易 iPXE 启动菜单

* * *

1.  [预启动 _ 执行 _ 环境](https://en.wikipedia.org/wiki/Preboot_Execution_Environment)集成部分 [↩](#fnref1)