# 在 Ubuntu 13.10 中找不到错误文件“/grub/i386-pc/normal.mod”

> 原文：<https://dev.to/adamkdean/error-file-grub-i386-pc-normal-mod-not-found-in-ubuntu-13-10-393c>

由于我越来越少使用 Microsoft 堆栈，越来越多使用 Node 堆栈，我决定在 Linux 上开发是一个好主意。Ubuntu 是工作中的选择，所以为了保持它的一致性，我想我应该在家试试。14.04 LTS 有一些奇怪的臭虫，所以我把 13.10 安装到我做的某个地方。

但是安装之后，grub 决定给我发一条讨厌的消息。

```
Error: file '/grub/i386-pc/normal.mod' not found
rescue> 
```

Enter fullscreen mode Exit fullscreen mode

我就知道这和 UEFI 有关。我讨厌 UEFI。SecureBoot 只会带来麻烦。我尝试了许多事情，但最后还是上床睡觉了。现在，我正在一个工作装置上写这个。我就是这样修好的。

我引导到一个真实的环境，打开终端，安装安装程序并进入其中。我将 Windows 安装在/dev/sda1 和/dev/sda2 上，并将 Ubuntu 安装到 Ext4 分区/dev/sda3:

```
$ sudo -s
# mount /dev/sda3 /mnt
# mount -t proc none /mnt/proc
# mount -t sysfs none /mnt/sys
# mount -o bind /dev /mnt/dev
# mount -o bind /tmp/ /mnt/tmp
# chroot /mnt 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我删除了 grub EFI 包:

```
# apt-get remove --purge grub-efi-amd64 grub-efi-amd64-bin 
```

Enter fullscreen mode Exit fullscreen mode

接下来我试图重新安装 grub pc 包，但是在解决`gb.archive.ubuntu.com`时出现了一个奇怪的错误。为了解决这个问题，我必须在`/etc/hosts`文件中添加一些条目:

```
91.189.91.15      archive.ubuntu.com
91.189.92.200     gb.archive.ubuntu.com
91.189.91.15      security.ubuntu.com
91.189.92.152     extras.ubuntu.com 
```

Enter fullscreen mode Exit fullscreen mode

现在我重新安装 grub pc 包:

```
# apt-get --reinstall install grub-common grub-pc os-prober 
```

Enter fullscreen mode Exit fullscreen mode

当屏幕弹出询问在哪里安装 grub 时，我选择了/dev/sda。之后，重启你的机器，你可能会很幸运。如果没有，你可以去 freenode 上的#ubuntu 询问，尽管你可能得不到答案。