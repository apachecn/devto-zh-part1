# 在 Raspberry Pi 上运行 Windows

> 原文：<https://dev.to/funkysi1701/running-windows-on-raspberry-pi-2868>

去年你可能还记得我谈到玩树莓皮。从那以后，我的树莓派就一直放在桌子上积灰。

本周，我参加了利兹夏普公司的活动，主题是**在 Raspberry Pi 上运行 Windows**,这再次激励我用 Pi 做一些事情。

但首先我学到了什么？

> 这里还有几张昨晚的照片[@ leedsharp](https://twitter.com/LeedsSharp?ref_src=twsrc%5Etfw)https://t.co/QvlsYjNFvB[# RaspberryPi](https://twitter.com/hashtag/RaspberryPi?src=hash&ref_src=twsrc%5Etfw)[# MSIoT](https://twitter.com/hashtag/MSIoT?src=hash&ref_src=twsrc%5Etfw)[pic.twitter.com/60o4wiiPSv](https://t.co/60o4wiiPSv)
> 
> –理查德·tasker(@ drawer)[2016 年 4 月 29 日](https://twitter.com/ritasker/status/725970415189909504?ref_src=twsrc%5Etfw)

我听说 Windows 10 的精简版可以安装在新的 Raspberry Pi 上，但我并没有真正理解 Windows 版本有多精简。现在，我们已经看到了操作系统由一个带有几个菜单选项的页面组成。

Windows 10 物联网的真正威力在于当你远程连接到它的时候。有几种方法可以做到这一点，PowerShell(查看[https://ms-IOT . github . io/content/en-US/win 10/tools/command line utils . htm](https://ms-iot.github.io/content/en-US/win10/tools/CommandLineUtils.htm)中的一些命令)，当然还有将 Visual Studio 连接到您的 Pi。

当我以前使用 Pi 时，它是使用 bash 脚本和 linux 命令。安装 Windows IoT 的好处在于，你可以编写 c#代码，这是我日常工作的一部分，所以理论上我应该会觉得更容易。

利兹夏普的演示令人印象深刻。如果你是《生活大爆炸》的粉丝，你可能还记得谢尔顿演奏的特雷门琴。实际上，用几个传感器和一个树莓派就可以构建一个特雷门琴。其代码在 github 上。

现在我受到了启发，我该怎么办呢？

我的树莓 Pi 不会支持 Windows 10 IoT，需要购买最新版本。我在考虑买一套工具，这样我就可以摆弄试验板、发光二极管和电阻了。也许不能马上造出一个机器人，但肯定会尝试做一些连接到 GPIO 引脚的事情。

如果你有任何建议，请在下面留言。