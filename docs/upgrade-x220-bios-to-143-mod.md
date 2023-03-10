# 将 X220 BIOS 升级到 1.43 mod

> 原文：<https://dev.to/dawncold/upgrade-x220-bios-to-143-mod>

原始 BIOS 有一些限制:内存频率，硬件白名单，缺少“高级”BIOS 选项等。

我从[这里](http://x220.mcdonnelltech.com/resources/)下载了修改过的最新 bios，如果你想用更快的 RAM 和 5G wifi(7620AC)升级你的 X220，你需要先升级你的 BIOS。

如果你有一个正在运行的 Windows，你可以节省一些时间来创建 WINPEu 盘，你应该按照 BIOS 升级 README.txt，如果没有，你需要创建一个 WINPEu 盘，你可以在 WINPE 上运行 BIOS 更新程序。

升级 bios 后，我无法启动我的 Ubuntu 系统，我发现了一个关于升级 BIOS 后引导加载程序丢失的解决方案，[这里是](https://askubuntu.com/questions/629734/will-updating-the-bios-remove-my-ubuntu-efi-entry)，需要注意的是，你必须使用 UEFI 模式，而不是 legacy 模式，所以检查你的 BIOS 引导模式，选择`UEFI only`，并运行在 LIVE 终端:

```
sudo efibootmgr -c -L Grub -l /EFI/ubuntu/grubx64.efi 
```

Enter fullscreen mode Exit fullscreen mode