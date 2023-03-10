# 穿越拱门——第一卷

> 原文：<https://dev.to/svettwer/traveling-through-the-arch--vol-1-25jh>

[![](img/9077dadf3254fad3ec717f9eef7cab78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CL7ZMY4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtCvTXVkWKMzF_uWXsk6XNQ.png)

在这一系列的博客文章中，我想和你一起经历我使用 Arch Linux 的经历，从最开始到(希望)快乐的结局。

正如我在第一篇博文中提到的，下周我将加入一家新公司。因为他们允许我选择我的操作系统，我将选择 Linux 发行版。我在个人电脑、上网本、智能手机等设备上使用 Linux。所以对我来说这是一个显而易见的选择。我每次问自己的唯一问题是，如果我想在新的硬件上安装一个 Linux，我应该使用什么发行版？

这一次，我将尝试 Arch Linux。不知道什么是 Arch Linux？没问题！我会试着给你一点概述我发现了什么！

目前我主要使用的是 Debian GNU/Linux T1、T2 Ubuntu T3 或它的衍生产品。它们都易于安装，几乎可以在任何系统上运行。如果需要一个更稳定的系统，而最新的技术不是必须的，我会选择 Debian GNU/Linux。就开发而言，我更喜欢 Ubuntu 或它的衍生版本，因为它们的软件包是最新的。尽管如此，Ubuntu 是一个非常沉重的发行版，它的 Unity 桌面消耗了大量的内存(带 Xfce 的 Xubuntu 也好不到哪里去)，它附带了很多我永远都不需要的软件，而且每个版本都有有效期(即使是 LTS 版本)。这就是 Arch Linux 发挥作用的地方。

Arch Linux 是一个非常轻量级的发行版，它允许您按照自己的意愿设置系统。你可以一个包一个包地选择在你的电脑上运行什么。公平地说:如果你使用最小的 Debian GNU/Linux 安装并手动完成剩下的设置，你也可以做到。

说到手动操作…

《Arch Linux 的[安装指南》是一个循序渐进的教程，告诉你如何只用一个可引导介质和你自己的双手来安装一个操作系统。如果你问我，这不是现在的工作方式。尽管我是一名软件开发人员，但我不想为我的操作系统安装设置所有的小零件。时间宝贵！如果我必须先手动设置我的操作系统，我应该什么时候写博客？

所以我不得不找些更聪明的。我的一个朋友向我推荐](https://wiki.archlinux.org/index.php/installation_guide) [Arch anywhere](https://arch-anywhere.org/) 。这个项目为一个名为 *Anarchy* 的基于 Arch Linux 的发行版提供了一个最小的安装程序，可以从官方的 Arch Linux 存储库中选择额外的包。所以我要试一试！

Arch Linux 的另一个优势是，它是一个[滚动发布](https://en.wikipedia.org/wiki/Rolling_release)的发行版。这意味着，只要您执行软件更新，就永远不会有过时的操作系统。对于您的操作系统版本，没有什么比支持限制更重要的了。每一个更新都被发送到每一个安装上。就前沿技术而言，这也很有趣。您将永远不会有问题获得最新的软件包为您的操作系统，因为没有版本号限制的支持或兼容性。

但是哪里有光，哪里就有阴影！

使用最先进的技术可能会因为不稳定的软件包或更新而导致系统崩溃。这是我对 Arch Linux 最大的担忧之一。我想要一个在任何情况下都稳定的操作系统。对我来说，失去一天的工作将是一场噩梦，因为我的操作系统因一些不兼容而崩溃。所以最小化破坏你系统的风险的唯一方法，就是经常更新，并在你更新之前访问[官方网站](https://www.archlinux.org/)和[论坛](https://bbs.archlinux.org/)来找到精彩的**。这至少需要一点时间和努力来让自己跟上时代。

我担心的另一点是，Arch Linux 正在使用自己的名为 [pacman](https://wiki.archlinux.org/index.php/pacman) 的包管理器。所以如果你要找的软件没有官方支持，你可能会有一些麻烦。尽管有[Arch User Repository(AUR)](https://wiki.archlinux.org/index.php/Arch_User_Repository)包含许多社区驱动的包，我不确定我是否能在那里找到我需要的一切。**

理论够了！让我们从下一集“穿越拱门”中的“动手”部分开始。

您想了解更多关于 Arch Linux 的信息吗？

详情请访问他们的[网站](https://www.archlinux.org/)！

本文最初发表于[medium.com/@SvenHettwer](https://medium.com/@SvenHettwer/traveling-through-the-arch-vol-1-f454e017646)