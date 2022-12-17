# 为什么融合硬盘和 Windows 不能混合使用

> 原文：<https://dev.to/yellowbrickcode/why-fusion-drives--windows-dont-mix>

我上班的机器是 2015 年末的型号 iMac，2016 年 8 月买的。它的规格还不错。主位，3.3GHz i7 处理器，16GB RAM 和 1TB Fusion Drive。那绝对应该涵盖我对开发工作的要求了吧？

我 99.9%的时间都在 Windows 中度过。由于我的机器有 16GB 内存(这是我希望 Windows 能够帮助 Chrome、Slack 和 Visual Studio 等内存密集型应用程序的内存)，所以很容易决定将 Windows 安装在 Bootcamp 分区，而不是安装在我必须与 OSX 共享资源的虚拟机上。

在设置好我的 iMac 后，我很快注意到它在 Windows 中有点慢。在相当长的一段时间里，我只是把这归因于我被我在家使用的 Macbook Pro 中的 SSD 所破坏。随着几个月的过去，我的工作要求变得越来越强烈，这已经开始让我经常发现它无法使用。我倾向于不关闭我的机器，而是让它进入睡眠状态。这样过了一个星期，关闭所有东西、重启并再次打开所有东西大约需要 25 分钟。即使它只运行了一天，也需要 12 到 20 分钟。

最近，我在大约 11 个月的时间里第一次启动了 OSX，并注意到它的运行速度快了很多，接近 OSX 在我自己的 Macbook Pro 上的表现。我决定是时候研究一下了，最终我找到了为什么我经常想把我的机器扔出我旁边的窗户的原因。

**当您在 Fusion Drive 上的 Bootcamp 分区上安装 Windows 时，Boot Camp 助理会在磁盘驱动器上创建 Windows 分区，而不是 SSD。**

这里是直接来自苹果-[https://support.apple.com/en-gb/HT201456](https://support.apple.com/en-gb/HT201456)-见第八个常见问题解答(在写作的时候)。

在某种程度上，我很高兴，因为我至少知道不仅仅是我不耐烦！不过，我也有点生自己的气。首先，在我们去苹果商店购买机器之前，没有对 Fusion Drive 的工作原理进行任何研究，也没有说明 Fusion Drive 是苹果的概念，因此可能在其他地方不起作用。

所以希望有人发现这一点，因为他们正在做我应该做的事情，**研究**！

我个人的观点是，如果你想在一个 Fusion Drive 上的 Bootcamp 分区中安装 Windows 来进行全职开发工作(或者任何中等强度的工作)，不要这样做。选择固态硬盘。它在我的 Macbook Pro 上运行起来像做梦一样！或者，如果你真的想/没有选择，选择 Fusion Drive，新款 21 英寸 iMac(和 27 英寸 iMac)现在可以拥有高达 32GB 的 RAM。我在 Fusion Drive 上的 Parallels VM 中试用过 Windows，它确实比在 Bootcamp 中运行得更好。因此，如果您选择融合驱动器，也要选择 32GB 的 RAM，并在虚拟机中运行 Windows，而不是在 Bootcamp 分区上运行。

*[这是 2017 年 6 月在我的博客上最初发布的](https://www.yellowbrickcode.co.uk/why-fusion-drives-and-windows-dont-mix/)*