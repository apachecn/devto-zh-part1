# 活在生活中

> 原文：<https://dev.to/highperformancecoder/living-la-vida-hackintosh-19dn>

像许多人一样，我以开源软件开发为生，我在 Linux 上开发，然后在 Windows 和 Macintosh 上构建。我确实有一台 Mac，一台相当可爱的 Mac mini，这是拥有该平台的一种经济有效的方式，但它也有一些缺点:

1.  我需要在最小安装的用户机器上测试我的软件，而不是我的开发人员机器上，以确保我已经捆绑了我的软件运行所需的所有必要的动态库。
2.  我需要构建一个 32 位版本的软件以获得最大的兼容性，而我的 Mac mini 是 64 位的
3.  我想在旅行时随身携带我的 Macintosh 环境，而不必将 mac mini 连同显示器、键盘等一起放在行李箱中。

是的，我知道，我可以买一台 Mac 笔记本电脑，但我并不特别喜欢 MacOS 作为我的开发环境，所以它仍然是一个额外的硬件。

所有这些问题的答案是将 MacOSX 加载到一个虚拟机上，比如 Oracle 的 Virtual Box，它是免费软件。最初，我将 Mac Mini 附带的 MacOSX Snow Leopard 发行版加载到 Virtual Box 中。这在 Virtual Box 的某些版本上有效，但在其他版本上无效，所以我不得不经常忽略升级 Virtual Box 的请求。然后我发现我可以在我的主要 Linux 计算机上运行 Vbox 映像，只要我不需要启动它，因为 MacOSX 只在启动时检查它是否运行在真正的硬件上。这是一个巨大的解放——我现在可以在我舒适的 linux 工作站上完成我的工作。

然后，不幸的是，升级发生了 Mac Mini 升级到 Yosemite，我的 Linux 机器升级到 OpenSUSE 13。随着升级，Virtual Box 也需要升级，因此虚拟机只能在 Mac Mini 上运行。不开心的一天。

但是现在我发现了托尼·麦克[http://www.tonymacx86.com](http://www.tonymacx86.com)的 iBoot 工具。这个伟大的工具允许人们在任何地方的虚拟机上安装一个“Hackintosh”，Macintosh 操作系统——这正是我所需要的。虽然苹果似乎对人们在虚拟机上运行他们的软件持悲观态度——但这正是我需要做的，所有其他选择都没有用。

让 iBoot 工作需要一点时间来适应。最重要的几点是:

1.  确保 EFI 引导已禁用。如果你告诉 Virtual Box 你正在加载 MacOSX，它会默认启用。
2.  其他可供选择的设置有 PA/NX、VT-x/AMD-V 和嵌套分页
3.  在“显示”下，选择 3D 加速和大约 20MB 的视频内存
4.  确保 SATA 类型是 AHCI
5.  另一个真正让我犯错误的是获得 iBoot 的正确版本。最初我下载的是 iBoot-3.3.0，没有效果。我必须做的是查阅/proc/cpuinfo 中的处理器信息，它告诉我:

```
Intel(R) Core(TM) i7-4770 CPU @ 3.40GHz 
```

然后我在维基百科上查找英特尔芯片，在“Haswell”页面上找到了该芯片的型号。所以我需要下载 iBoot-Haswell-1.0.1，这就成功了。

使用 iBoot.iso 文件，将其放入虚拟 DVD 驱动器，并启动虚拟机。如果您已经在 VM 中安装了 MacOSX，您可以使用右箭头键来选择它并引导它。因为这是我发现自己所处的情况，所以我就这么做了。但是，如果不这样做，只需用 MacOSX 安装盘替换 iBoot.iso 映像，并引导它。

就是这样。我现在正在克隆我的一个虚拟机并将其升级到 Yosemite！祝我好运。