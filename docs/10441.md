# Cloudready 或如何在非 Chromebook 笔记本电脑上安装 Chrome 操作系统

> 原文：<https://dev.to/leonardofaria/cloudready-or-how-to-get-a-chrome-os-in-a-non-chromebook-laptop-6lm>

几个月前，我在 The Verge 上看到一篇关于 [CloudReady](https://www.neverware.com/) 的[帖子](http://www.theverge.com/2016/3/7/11173836/neverware-cloudready-how-to-convert-pc-into-chromebook-free)。CloudReady 是 Chromium OS 的翻拍版，可以在旧 PC 或 MAC 上使用。产品背后的公司向有兴趣继续使用其硬件的组织销售其解决方案/支持。真的很有意思。

我写这篇文章是因为我的妻子有一台旧的三星笔记本电脑，我终于找到时间给 CloudReady 一个机会。她的笔记本电脑最初运行的是 Windows 7，后来更新到了 Windows 10。想法是在另一个分区中使用 CloudReady，并像任何 Linux 发行版一样使用 Grub 来处理引导过程。

可惜这个过程比我想象的要难。事实上，CloudReady 支持[双引导安装](https://guide.neverware.com/install-cloudready/#select-dual-boot-or-standalone)，但有一些细节需要注意:

*   如果您使用的是旧电脑，您可能需要将 Windows BIOS (MBR)转换为 UEFI。一旦微软 Technet 有了一篇关于它的详细文章，我就不再描述它了。
*   我最初为 Windows 将硬盘划分为 1 个分区，并为 CloudReady 留出了未分配的空间。Windows 10 安装在其分区没有问题。
*   最后一步是从闪存驱动器启动 CloudReady，并根据它的[文档进行安装](https://guide.neverware.com/critical-requirements/)双重启动非常有效。

我的第一印象是 CloudReady 比 Windows 更快更流畅。除此之外，没有无限的更新，没有恶意软件，没有我们在 Windows 中习惯看到的一切蹩脚的东西。这绝对是让一台旧电脑复活的好方法。一件糟糕的事情发生了，CloudReady 停止工作，我需要重做所有的安装过程。