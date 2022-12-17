# 将窗口添加回 grub

> 原文：<https://dev.to/adamkdean/adding-windows-back-to-grub-3npp>

在我的上一篇文章之后，我坐在这里安装了一个 grub，但是它还不允许我启动进入 Windows。

这真的很简单。

```
$ sudo update-grub
[sudo] password for adam: 
Generating grub.cfg ...
Found linux image: /boot/vmlinuz-3.11.0-22-generic
Found initrd image: /boot/initrd.img-3.11.0-22-generic
Found linux image: /boot/vmlinuz-3.11.0-12-generic
Found initrd image: /boot/initrd.img-3.11.0-12-generic
Found memtest86+ image: /boot/memtest86+.bin
Found Windows 7 (loader) on /dev/sda1
done 
```

Enter fullscreen mode Exit fullscreen mode

完成了。