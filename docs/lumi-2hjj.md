# 卢米

> 原文：<https://dev.to/ladvien/lumi-2hjj>

### 2016 年 3 月 29 日更新

我已经开始把整个程序重写为一个 Windows 应用程序。这不是我的选择。看起来微软推出的 BluetoothLE API(说实在的，太糟糕了)在 Windows 通用应用工具链下最容易被采用。

无论如何，这里是新项目的休息空间

[米露](https://github.com/Ladvien/Lumi_Windows_App)

### 米露为桌面

在我来的地方，他们有一句谚语:“视频或它没有发生。”我一直喜欢这句话。这意味着我们必须把工作放在打字的地方。这是我编写的上传程序的视频，用来与 TinySafeBoot AVR 双线(UART)引导程序接口。

它是基于这样的想法编写的，即可以使用蓝牙 4.0 从计算机直接打开串行连接。或者，如果我们变得超级花哨，使用 WiFi。在接收端，连接到 AVR 的分别是 HM-10 或 ESP8266。这将允许直接无线上传 Arduino 草图(或 Atmel studio hex 文件)到目标 Arduino(或 AVR，如果你愿意)。

它有几处缺陷，但我相当满意。

有关项目的更多信息:

[http://ladvien.github.io/robots/tsb/](http://ladvien.github.io/robots/tsb/)

整个项目是以我去年去世的朋友米露的名字命名的。一个对摄影很有品味的人。:)

源代码:

[https://github.com/Ladvien/Lumi_TinySafeBoot_Uploader](https://github.com/Ladvien/Lumi_TinySafeBoot_Uploader)