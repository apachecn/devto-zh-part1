# 清理和压缩您的 VDI

> 原文：<https://dev.to/msanford/clean-up-and-compact-your-vdi-4bml>

### TL；速度三角形定位法(dead reckoning)

```
Guest OS: sudo sfill -llvz /

Host OS: VBoxManage modifyhd --compact \<\_.vdi\> 
```

Enter fullscreen mode Exit fullscreen mode

### 详细信息

几个月来，我的 VirtualBox 瘦服务器在客户操作系统数据库导入、静态资源创建、临时文件创建、日志循环和一堆我不需要无限期存储的其他东西上增加了几磅。每当需要调用更多扇区在来宾系统中存储内容时，VirtualBox 都会相应地增加物理 VDI 映像，直至达到指定的最大大小。但是，当您从客户系统中删除文件时，VDI 不会相应收缩(这是有充分理由的)。

如果您已经实施了备份策略(就像我在 Time Machine 上所做的那样)，您可能不希望每次一个位发生变化(例如，当您启动虚拟机时)就备份一个 30 GB 的 VDI 文件。当您删除了旧的数据库、资源和日志，并且客户操作系统实际上只代表大约 5 GB 的数据时，这种情况尤其明显。

VBoxManage modifyhd 允许您压缩物理 VDI，但有一个重要的先决条件，它只能从 VDI 中截断来宾中的*零*:

> 使用— compact 选项，可用于压缩磁盘映像，即删除只包含零的块。这将再次缩小动态分配的图像；它将减少映像的物理大小，而不会影响虚拟磁盘的逻辑大小。压缩既适用于基本映像，也适用于作为快照一部分创建的差异映像。

因此，仅仅从您的客户那里删除一个文件并不能让您回收空间。您必须对来宾操作系统卷上的可用空间进行零填充。

在你的访客中(这里是 LTS 的 Ubuntu Server 12 . 04 . 02)

```
sudo apt-get install secure-delete sudo sfill -llvz / 
```

Enter fullscreen mode Exit fullscreen mode

-ll 选项只写一遍(而不是默认的 38 遍)，-z 选项用零替换来自/dev/urandom 的一遍数据，这正是 VBoxManage 所寻求的。

在你的主机:

```
VBoxManage modifyhd --compact \<virtual\_disk\_file.vdi\> 
```

Enter fullscreen mode Exit fullscreen mode

我一步就把我的从 25 GB 缩小到了 5 GB。我的固态硬盘允许我在不到 90 秒的时间内在来宾中零填充大约 20 GB 并压缩 VDI。

*原载于 2013 年 8 月 8 日 michaelsanford.com***。**