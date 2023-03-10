# 如何使用 CLI 创建 Ubuntu live USB

> 原文：<https://dev.to/lobo_tuerto/how-to-create-an-ubuntu-live-usb-using-the-cli-1e5p>

* * *

你可以查看这篇文章的最新更新版本，在 [lobotuerto 的笔记-如何使用 CLI 创建 Ubuntu live USB。](https://lobotuerto.com/blog/how-to-create-an-ubuntu-live-usb-using-the-cli/)

* * *

如果*启动盘创建者*应用程序对你不起作用，或者花很长时间才能完成，或者总是在最后出现错误…你为什么不尝试从 CLI 部署 Ubuntu ISO？

超级简单，看看怎么做。

*确保准备好一个`.iso`文件，或者从[这里下载最新的 Ubuntu 镜像。](http://releases.ubuntu.com/)*

# 为您的设备找到正确的字母

将记忆棒插入一个端口，用这个获得一个已连接设备的列表:

```
sudo fdisk -l 
```

Enter fullscreen mode Exit fullscreen mode

在输出中识别您的驱动器的字母，在我的例子中，我要寻找的驱动器是这个:

```
/dev/sdb1 8192 15636863 15628672 7.5G c W95 FAT32 (LBA) 
```

Enter fullscreen mode Exit fullscreen mode

我能看出来，因为它是一个 8GB 的设备，看看上面报道的 7.5GB 的大小。

在本例中，驱动器的字母是来自`sdb`的`b`。可能是`c`、`d`或者别的什么。只需**确保**它确实是您想要丢弃的驱动器。一旦它被归零，你将无法从中恢复任何东西。_

# 将 u 盘中的位清零

接下来，让我们*归零*该驱动器，这作为一种安全措施，并且它还允许您判断该驱动器是否仍然工作正常——如果该驱动器正在死亡，该过程将花费不寻常的长时间，或者它将在到达终点之前被卡住，这是您需要获得新 USB 的信号。

好，让我们用
在 u 盘上写满零

```
sudo dd if=/dev/zero of=/dev/sdX bs=4M status=progress conv=fdatasync 
```

Enter fullscreen mode Exit fullscreen mode

它会向你汇报进展。

# 将 ISO 镜像复制到 u 盘

完成后，转到下载**的目录。iso** 文件，并像这样部署到 USB:

```
sudo dd if=ubuntu-image.iso of=/dev/sdX bs=4M status=progress conv=fdatasync 
```

Enter fullscreen mode Exit fullscreen mode

就这样，现在你应该有一个可启动的 USB，里面有 Ubuntu！