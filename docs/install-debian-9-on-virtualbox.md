# 在 virtualbox 上安装 debian 9

> 原文：<https://dev.to/dawncold/install-debian-9-on-virtualbox>

# 为什么选择 virtualbox

**更新:VMWare OS 版本列表仅帮助您设置基本资源(CPU、内存等)，因此您可以选择任何人并根据需要修改它们**

我不知道应该从 VMWare OS 版本列表中选择哪个 OS 版本。有一些 Debian 系列，最新的是 Debian 8.x，但我需要 9，列表上也没有 Kernel 4.x 项目，所以我安装了 virtualbox，我找到了一个 Debian(64 位)。

# 磁盘空间

最好生成 15Gib 以上的磁盘。我使用了一个 10Gib 的磁盘，在安装 GNOME 时出现了“没有更多空间”的错误。

# 附加软件

编译 virtualbox 附加软件需要一些构建软件，可以从`build-essential`包中获取，构建内核模块需要内核头文件，可以从`linux-headers-4.9.0-3-amd64`中获取，默认内核版本。

```
sudo apt install build-essential linux-headers-4.9.0-3-amd64 
```

Enter fullscreen mode Exit fullscreen mode