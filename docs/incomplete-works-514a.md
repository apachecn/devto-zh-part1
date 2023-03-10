# 未完成的作品

> 原文：<https://dev.to/ladvien/incomplete-works-514a>

我张贴这个集合是出于沮丧，也许是失败。在过去的两个月里，我一直在做几个项目，试图完成一些事情。我已经沉迷于那句“有用！”我想任何人看到 LED 闪烁时都会有这种感觉。遗憾的是，我觉得这些项目中的大部分都失败了。

我张贴的第二个原因是后代。

我已经开始欣赏失败，因为我从失败中学到了很多。当然，我更愿意从别人的失败中吸取教训。所以，我想我应该试着为别人写下我所有的错误。

最后一个原因是战术上的谦逊。

我想，如果有人告诉我到底哪里做错了，我也许能完成其中一些项目(不过，如果有人告诉我什么是对的，可能会花更少的时间)。

好了，自我厌恶和唠叨够了。

**内容:**

1.  **Arduino 心率传感器。**
2.  **蓝牙 4.0**
3.  **大功率 LED 散热**
4.  **XL4432 -远程射频**
5.  **SMD 版 Atmega Fuse Doctor**
6.  **Arduino 恒温器**
7.  **树莓 Pi 和远程编译**

**脉搏传感器再造——心碎的故事:**

**脉搏传感器尝试 1:**

[![](img/15b9cefa43967b6b914403ebb55a5f4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tQJS4qH3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/PulseSensor_1.jpg)

一段时间以来，我一直对生物统计学感兴趣。我想，如果我的妻子完成了研究生学业，成为我的甜心妈妈，那么，我会继续我在实验心理学方面的愚蠢研究。开发我自己的传感器，或者至少对它们的工作原理有深入的了解，可能会帮助我进入一个项目(该死的教育膨胀)。所以，我一直在关注开放硬件传感器，看起来这些[家伙](http://pulsesensor.com/open-hardware/)脉冲传感器越来越受欢迎。

和往常一样，我仍然相信像我这样的笨蛋学习聪明东西的最好方法，是把真正聪明人做的东西拆开。但作为一个不墨守成规的人，我避免尝试重新制作他们的传感器。尽管如此，在查看了我找到的其他原理图( [1](http://embedded-lab.com/blog/?p=7336) 、 [2](http://www.seeedstudio.com/wiki/Grove_-_Heart_rate_ear_clip_kit) 、 [3](http://www.youtube.com/watch?v=qNBTlvJr8vE) 、 [4](http://www.radiolocman.com/shem/schematics.html?di=144220) 、 [5](http://www.jamesrosko.com/2012/01/arduino-pulse-monitor.html) )后，我决定我会是这样一个不墨守成规的人，我会遵从并使用流行的传感器。

看了一眼，好像很棒；兼容 Arduino 的小型轻量级心率监测器。然后，我注意到 [**设计文件**](http://pulse-sensor.googlecode.com/files/PulseSensorAmpedHardware.zip) 被放在了 [**设计火花**](http://www.designspark.com/) 中。“没问题，我想，我只要把它们导出到 Eagle，然后我就可以玩 PCB 布局了，也许可以把那些 0603 换成 0805，然后送到 OSHPark。”

发现从 Design Spark 导出 Eagle 文件并不容易。

新的计划，我将只是按照脉冲传感器[示意图](http://pulse-sensor.googlecode.com/files/PulseSensorAmpd%20-%20Schematic.pdf)和重新创建整个董事会在鹰(所有半英寸)。我就是这么做的。我想把那些老鹰的文件贴出来，但是，它们太烂了，没用。我犯了几个重大错误。

[![](img/e8a17927d9bab969b7a4fe1debbc5a75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X0UNAHgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Pulse_Sensor_1_--_Top.jpg)

[![](img/5af11b79c70bd614feba5eaf4fe28bee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NPMhBdjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Pulse_Sensor_1_--_Bottom.jpg)

首先，我必须为电路板创建几个 Eagle 组件。运算放大器、LED 和光传感器。分别是 [MCP6001](http://ww1.microchip.com/downloads/en/DeviceDoc/21733j.pdf) 、 [AM2520ZGC09](http://pulsesensor.myshopify.com/pages/open-hardware) 、[T5、【APDS-9008](http://www.avagotech.com/docs/AV02-1169EN) 。没有一个是真正的威胁。我根据数据表制作每个零件。然后，我把 Eagle 中的原理图串起来，切换到 PCB 布局，把我的棋子扔了下去。但出于某种原因，我想，“我应该用 0402 替换这块板上的 0603 被动。”

我想，如果我能把板子再缩小一点，我会更开心，对吧？我是说，越小越好——女人们都这么说。好吧，这是我唯一不后悔的事情。

最后，这块板被送到了奥什帕克，只收了 2 美元。

正如我湖对面的朋友所说，当邮件来的时候，我对这个小小的板子感到很兴奋。不幸的是，在我填充了公告板之后，我开始了一连串的失望。

[![](img/219f8ad0a6ec050c405b26c7c9cb8cdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ddjAaxVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0569.jpg) 就像我说的，我一点也不后悔 0402s。他们都很容易焊接。不过，我认为我对 0402 而不是 0802 的主要不满是在无镊子焊接方面。看，当我去焊接 0802s 时，我有这样一个过程，用一点焊料轻敲一个焊盘，以一个电阻为例，用镊子在我的左手握住 0802 的身体，同时我的右手保持焊料温暖。然后，我简单地将电阻的一端插入焊料池中，移开烙铁，然后，当焊料冷却时，用镊子松开。为了得到另一端，我会放下镊子，简单地用焊料敲击另一端。*瞧*。

我会试着拍下这个过程，但是，我还没有第三只手。虽然， [**这些民间的**](http://www.kickstarter.com/projectss/antonyevans/glowing-plants-natural-lighting-with-no-electricit) 都在为此努力。

这对 0402 没用。第一，用镊子夹住 0402 的尸体就像一个巨人试图在一根牙线上走钢索。但问题真正开始于第二步，当我放下镊子轻敲另一端时，来自我的熨斗的热量通过小 0402 射向另一端，松开硬化的一面，这一发生后，整个 0402 直立起来，拥抱我的熨斗。当然，这以我迅速用手指把小家伙拉下来结束(不聪明，但每个 0402 就像 0.20 美元)。

[![](img/5bf611a183936b7d6f3e99df1226e988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w4570QRF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8776.jpg) 几个笔记:

LED 可以穿过 PCB 上的孔，但我担心钻头不够宽(我猜测了一点)。

OSHPark 采用非方形的形状，不过他们会像方形一样收费。

开源硬件与开源(但缺少一些关键信息)硬件不同。我相信脉搏传感器是后者。

唯一不能用烙铁焊接的是光传感器。它的所有焊盘都隐藏在实际组件下。所以，我用了老式的熨斗。

不管怎样，一旦我把所有的组件都准备好了？我扔上几根电线，把它连到 Arduino 上，上传草图，打开它。*和*...**烟。**

啊啊啊。

我想告诉你这个故事有个好结局。但是，正如标题所示，这是一部不完整的作品。

我开始解决电路板的问题:我检查了布线、原理图和我制作的 Eagle 组件。我试过 3.3v，不同的草图，摸传感器，不摸传感器，向克苏鲁祈祷。没什么。只是很浓的烟味。

最后，我放弃了。

(嗯，就像我曾经放弃任何东西一样。愚蠢的执着天性。)

* * *

**脉搏传感器尝试 2:**

好吧，几个星期过去了，我还在做其他的项目，但是那个小小的脉冲感应板一直在乞求另一个机会。

我决定再试一次。我不会再试图无知地排除缩略图大小的电路板的故障，所以我回到设计师的原始文件，下载 DesignSpark，并调出原理图和电路板。在简单地检查了它们之后，我开始意识到我在棋盘布局上有点偏离。然后我突然想到，我总是可以手动复制设计，这不会花很长时间，因为我已经把原理图和组件做好了。

嗯，下面是**那个过程的视频**:

[https://www.youtube.com/embed/hZI9QOGVHNM](https://www.youtube.com/embed/hZI9QOGVHNM)

在我发现两个错误(错误的 MCP6001 和 APDS-9008 的迷失方向)后，我感觉好一点了，再次把它发送到 OSHPark。两美元后，我不太确定了，但不管怎样，板子还是上路了。在我等待的时候，我跳到德州仪器的网站上订购了(我希望是)正确的运算放大器样品。

当主板进来的时候，我做了一件事后让我后悔的节俭的事情:我把旧主板上的组件拆下来放在新主板上。这在当时听起来像是一个伟大的财务策略，但当我意识到我的新的小董事会仍然不工作时，增加了更多的工作。现在，我不得不质疑是组件、主板，还是我也碰到了这篇[文章](http://makezine.com/2012/02/17/reel-crime-the-pulse-sensor-counterfeit-leds-story/)，吓得我魂不附体。尽管如此，这让我对那些人充满敬意。在一个晚上重新焊接 2000 个 SMD leds 不是一个小任务。也许这让我产生了一丝内疚，因为我一直在努力规避他们的*比他们 25 美元[传感器](http://pulsesensor.myshopify.com/products/pulse-sensor-amped)应得的利润更多。*

到目前为止，差不多就是这样。我没有时间再给她一次机会，但我会的。下一轮，我将为主要组件创建分线板，以确保我的焊接没有问题。我真的只关心光传感器，因为垫实际上是 DFN(没有暴露的腿)。但是我希望在接下来的一周到一个月内完成这件事。

1.  **.29** ( [**数字键**](http://www.digikey.com/product-detail/en/MCP6001T-I%2FOT/MCP6001T-I%2FOTCT-ND/697158) )

2.  光传感器: **1.23 ( [数码键](http://www.digikey.com/product-detail/en/APDS-9008-020/516-2662-1-ND/3909167) )**

3.  LED: **.79 ( [数码键](http://www.digikey.com/product-detail/en/AM2520ZGC09/754-1423-1-ND/2163781) )**

4.  0603 肖特基二极管* *: .50 ( [数码钥匙](http://www.digikey.com/product-detail/en/CD0603-B0130L/CD0603-B0130LCT-ND/3438043?WT.mc_id=PLA_3438043) )* *

5.  无源:~ **2.50** **-电阻:1 x 470k，1 x 12k，2 x 100k，1 x 10k，1 x 3.3 兆欧-电容:3 x 4.7uF，2 x 2.2uF**

6.  OSHPark 板* *:* * $ **.67** (最少 3 块板，成本 2.00 美元。3/2.00 = ~.67)

**总计(近似值):5.98 美元**

* * *

**HM-10 -蓝牙 4.0 模块-破局突围的故事**

**更新(8-3-13)**

我已经改正了这个问题；是的，它是天线下面的地平面。

这里是一个更新的版本。T3】

如果有人了解我，他们知道我很贱。但我更愿意认为自己是“资源高效型的”这让我在 FastTech 买了点东西。他们的东西没有易贝便宜，但也差不多了。

好了，这是这个故事的前奏。不久前，一位同事手机上的耳机插孔坏了。他说，“你不要乱动那些东西？你应该给我做一个蓝牙耳机。”对外:玩笑式偏转。内部:接受挑战。

我开始寻找不同的蓝牙芯片。我的意思是，当你可以用两倍的价格制造一套耳机的时候，为什么要花不到 20 美元买一套蓝牙耳机 ？尤其是，当只需要大约 100 小时的“摆弄”时间？美国人太懒了。

嗯，我登陆的第一个 IC 就是这个人: [LMX9838](http://www.ti.com/lit/ds/symlink/lmx9838.pdf) 。直到我完成了鹰的部分，设计了原理图，并在电路板上工作，我才知道它的零售价[是多少。嗯，我不是每次想在我的项目中嵌入蓝牙时都要订购样品，此外，蓝牙 2.0 不是很耗电吗？](http://www.digikey.com/product-detail/en/LMX9838SB%2FNOPB/LMX9838SB%2FNOPB-ND/1730309)

好吧，回到看集成电路。

在第二次浏览时，我偶然发现了德州仪器的新 [CC2500](http://www.ti.com/lit/ds/symlink/cc2500.pdf) 系列。正义。

我看到了用这个小芯片 [CC2540](http://www.ti.com/lit/ds/symlink/cc2540.pdf) 制作蓝牙 4.0 设备的可能性。这是一个具有蓝牙 4.0 功能的 SoC(片上系统)。再说一遍，正义。

我从 TI 订购了几个芯片样品。在我等邮件的时候，我开始涉猎所有的术语:BLE、BT 4.0、智能能源等等。我搜索了一下，看看是否有人已经完成了驱动这些小家伙所需的原理图、PCB 和固件。以下是我的一些发现。

**硬件:**

[![](img/224ad860d5c589bb857ebdc622ad6933.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w-KAviYg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/500px-CC2541_Breakout_Board.jpg)

这里有一个 [**完整的 PCB**](http://processors.wiki.ti.com/index.php/CC2541_Breakout_Board) 给 [CC2541](http://www.ti.com/lit/ds/symlink/cc2541.pdf) 看起来是针对低功耗优化的。我要说的是，整个芯片在 TI 论坛上都有很好的记录，但总的来说，硬件方面是最少的。

我下载了老鹰文件，并开始撕掉所有不必要的电路。(我觉得有 lipo 充电器电路？)目标是将电路板尺寸做得足够小，以降低印刷成本。

但是当我开始撕开靠近触角的部分时，我注意到把整个事情搞砸是多么容易。由于我还没有造出我的 [**频谱分析仪**](http://scottyspectrumanalyzer.com/) ，每次订购一个版本的电路板，我都会在黑暗中摸索。

[![](img/9188338dd96c34c84e6dbb247753aa5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GsHP7_t0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/cc2541_Breakout_PCB.jpg) 在所有 0402 和 DFN 封装的基础上实现这一点，嗯，我决定我想在一个完整的板上玩芯片，在我投入时间进行个人开发之前已经安装了固件(花哨的说法是:**我变懒了**)。

我不会涵盖固件或软件，因为我走了另一条路，并没有彻底谷歌搜索他们。但是要知道，在德州仪器的 cc2500 论坛中，几乎有你想要的一切。不过，要做好准备，如果你想创建自己的固件，你需要温习一下 C 和 AVR 编程。

这让我回到了 Fasttech。

我注意到有一天他们有这些[**HM-10**](http://www.fasttech.com/products/0/10004051/1292002-ti-cc2540-bluetooth-40-ble-2540-transparent-serial)在打折。**哇哦！**预设计的 CC2540 板，已经创建了固件？可命令的固件？我买了两个。

我想我至少可以感受一下芯片，看看它是否是我真正想花时间开发的东西。

一周后，我有了这些小家伙。

[![](img/c63f5fe33ff224fa193edcacc30be2bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lOAYmJ2z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/HM-10-2.jpg) 他们是不坏的小板子。但是，他们很小。我试着把跳线焊接到蚂蚁大小的凹槽上，结果不太好。我知道要真正开始玩棋盘，我需要突破。

所以我做了一个:

**[HM-10 突围 v.9](http://www.ubermentis.com/files/HM-10%20Breakout%20v.9.rar) (不用，烂透了)**

当分线板进来的时候，我很惊讶它们居然起作用了(当我做的东西变成现实时，我通常会很惊讶:)。

他们很好地跨在我的面包板上。它让我可以随心所欲地使用电路板上的所有部件:V、GND、Rx、Tx 和 PIO1(连接状态 LED 的引脚)。

[![](img/e53a6a0f592419527011d6e04d8db9d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--koyGhUlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0666.jpg)

由于小 HM-10 在 3.3v 下工作，我小心翼翼地把它放在试验板上，并拿出我的[**spark fun lily pad FTDI**](https://www.sparkfun.com/products/10275)(第一个巨大的错误)与电路板的串行接口。

我不知道哪里出了问题。电路板不会响应 AT 命令(我用的是[实数](http://realterm.sourceforge.net/))。我的意思是，我已经将 3.3v 插入 HM-10 的电源，所以我知道它没有获得太多的电压。我甚至用万用表检查了一遍(大概一百遍)。

好吧，作为你们当中比我聪明的人(所以你们所有人？)大概已经知道了:Sparkfun 的 Lilypad FTDI 是为了给 Lilypad 提供 3.3v 的电压而设计的，但是 Lilypad 上的 Atmega-328-P 实际上是可以承受 5v 电压的。那么，为什么要降低 Rx 和 Tx 线上的电压呢？当然，这不是我花了几个小时得出的结论，真的，直到我开始用万用表随机探测电路板。

该死的。

好吧，13.98 美元没了(是的，我慢得足以杀死两块板)。

沮丧之余，我又订购了两块板。

你看，说到电子设备，我的驾驶守则是...嗯， [**这家伙解释得更好**](http://www.youtube.com/watch?v=nU2y6ztlMAQ) 。

还买了 real[**FTDI breakout**](http://www.ebay.com/itm/400405460289?ssPageName=STRK:MEWNX:IT&_trksid=p3984.m1439.l2649)和一个 [**逻辑电平转换器**](http://www.ebay.com/itm/261145190601?ssPageName=STRK:MEWNX:IT&_trksid=p3984.m1439.l2649) 。我厌倦了油炸食物。

当所有东西都进来时，我用热风枪加热突围底部，直到 HM-10 松动，从而将破碎的木板拿掉。我用一些焊芯清理了分线板的顶部。然后，焊接新的 HM-10s。

**将 HM-10 焊接到分线点的视频**

[https://www.youtube.com/embed/BOT7Vx9lAlk](https://www.youtube.com/embed/BOT7Vx9lAlk)

我把所有的乱七八糟的东西都布线在一块试验板上，当我真正收到回复时，我感到很惊讶。

在(某时间或时刻);在(学习或工作地点);在(某处)

AT+OK

* *胜利！** (嘿，我已经学会享受哪怕是小小的胜利)

我不得不使用 Realterm 中的特定设置来成功地与 HM-10 通信

在“端口”选项卡下

*   9600 英镑
*   奇偶校验:无
*   数据位:8
*   停止位:1
*   硬件流控制:RTS/CTS
*   软件流控制:接收-是，发送-是

在“回显端口”选项卡下

*   回声打开:是
*   监视器:是

然后，在“发送”选项卡下键入我的 AT 命令并点击“发送 ASCII”:

这非常有效。每次我输入“AT ”,它都会返回“AT+OK”

于是，我开始挖掘 AT 命令的其余部分。我确实这么做了。

显然 HM-10 是由**研制的。这些人在济南的 [**高新技术开发区**](http://rightsite.asia/en/industrial-zone/jinan-hi-tech-industrial-development-zone) 的某个地方。无论如何，幸运的是我们有谷歌翻译，我能够通过他们目前的几个文件。但在此之前，我曾浪费时间试图让模块响应不再受支持的 AT 命令(比如 connection 命令)。**

 **最终，我找到了现在的英文手册

[**HM-10 手动**](http://www.ubermentis.com/files/HM-10_English.pdf)

[![](img/340fc6ad7b33ac41c23a17f4ac9178c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wWz_w4ZR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8991_2.jpg) 这本手册回答了我很多问题。它带有完整的引脚排列(甚至是原理图！).玩了这些命令之后，我重新命名了这个模块，重新设置了它，并运行了许多其他需要的命令。

现在进行现场测试。

我得到了我的工作电话，iPhone 4S，它配备了蓝牙 4.0。我试着使用设置下的蓝牙连接，它找不到我的小 HM-10。我切换到[浅蓝色](https://itunes.apple.com/us/app/lightblue-bluetooth-low-energy/id557428110?mt=8)，不仅能够找到我的小模块(名为 Bob)，而且它连接上了，并允许我向 Realterm 发送串行数据！**成功。**

我以为我正在把这些小 HM-10 拍在机器人上，在我的 PC 上插上蓝牙 4.0 加密狗，坐下来让奇迹发生。事情不完全是这样的。我订购了[这个](http://www.fasttech.com/products/0/10000148/1018901-ultra-mini-bluetooth-csr-40-usb-dongle-adapter)蓝牙加密狗，当它进来时，我很快发现为它提供魔力所需的驱动程序不可用。我都试过了，TI 的工具包，随机上网驱动，黑幕上网驱动。有了那个加密狗就不会发生这种事了。

[![](img/61cec4ecee6d4132c002591822b99321.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6oYv6nmm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0063.png)

我想这就是你买你能找到的最便宜的电子狗的结果。所以，我转到新蛋，买了这个加密狗，确保它有支持的驱动程序。

当我把它放进去时，它仍然不能工作(我相信这完全是一个软件问题，所以如果我在 Linux 机器上使用这些加密狗，我希望会有不同的结果)。

我想，“管它呢，我总是可以把一个微控制器、RS232 和另一个 HM-10 做成我自己的加密狗。”

嗯。但是我不知道如何把两个模块连接起来。我把它们都放在试验板上，它们都有一个闪烁的小 LED(意味着没有连接)，但这些小家伙就是不亮。

于是，我心血来潮，给 Jnhuamoa 发了封电子邮件，询问。

> 你好，
> 
> 我正在连接你们的两个 HM-10 模块。我遇到了麻烦，因为似乎没有配对命令。我用“AT+VERS？”它告诉我，我正在使用版本 HMSoft V303。这是旧固件吗？如果是，是否有更新的固件可供我下载并写入 cc2540？我看了你的网站，我似乎找不到任何固件升级。但是，我只看英文，所以我很好奇，如果我错过了它的翻译。
> 
> 我很感激你给我的任何帮助，
> 
> 托马斯·布里泰因

令我惊讶的是，他们回应了

> 亲爱的先生
> 
> 感谢您选择我们的产品。
> 
> 在两个 HM-10 模块之间，配对和连接过程是自动的。
> 
> 您只需确保 HM-10 模块中的一个设置为主机模式，另一个设置为从机模式(使用 AT+ROLE 命令)，并且两个模块的类型设置相同(使用 AT+TYPE 命令)且引脚编号也相同(使用 AT+PASS 命令)。
> 
> 如果主模块已经连接到其他模块，请先执行 AT+CLEAR 命令。
> 
> 我们的网站有模块工作流程图，你可以看看。
> 
> :)
> 
> 致敬
> 
> HMSoft
> 
> guocg

但是...现在怎么办？我的意思是，我可以把这些人连接到一个 Arduino-bot 上，但是每个机器人只有一个加密狗。我想要的是每个加密狗有几个蓝牙机器人。

老实说，我从未想过要用蓝牙作为机器人的系绳，我只是在寻找一个除了我同事的耳机之外的应用。

在多读了一些手册，并修改了 AT 命令之后，我又给 Guocg 发了一封电子邮件。

> 好先生，
> 
> 谢谢你的快速回复。
> 
> 听了你的指示，我觉得自己很愚蠢。不到一分钟我就把 HM-10 配对好了。一个非常简单的过程。谢谢你。
> 
> 但是我确实有一些其他的问题。有什么方法可以更好地控制连接过程？我真的希望有一个微控制器(PIC，Atmega)在中间给 HM-10 非常具体的命令，这将涉及主机连接到几个从机取决于主机的需要。我知道如何改变 PIN，但是一个主机实时管理几个从机的速度够快吗？
> 
> 这是我将要尝试的过程:
> 
> 1.  设置 3 个具有唯一引脚的从机
>     
>     
> 2.  设置 1 主机连接到微控制器。
>     
>     
> 3.  将主机设置为 AT+IMME0(给定命令时工作)。
>     
>     
> 4.  微控制器将拉动 HM-10 复位线，然后发出以下命令:
>     
>     
> 
> a.在+清除时
> 
> b.AT+PINslave1
> 
> c.在+工作
> 
> 1.  微控制器将向 slave1 发送一个“X”
>     
>     
> 2.  Slave1 将有一个微控制器每半秒左右拉一次复位线，除非它与“X”串连接。
>     
>     
> 
> 我不确定这个过程的速度，但我相信它会让我远程切换模块。我读过一些旧版本的 HM-10 模块固件。还有机会得到那些吗？我现在明白了，与 HM-10 模块配对非常容易，但似乎也非常受限制。
> 
> 谢谢你的帮助，
> 
> 托马斯

这一次，我想我得到了一个吹掉的回应。这是公平的。我是个黑客，不是工业开发者。

> 亲爱的先生
> 
> 你应该确保+型值的 HM-10 模块为 1。密码设置命令为+PASS。
> 
> 致敬
> 
> HMSoft
> 
> Guocg

[![](img/b5af9d29112271eba29eda9b83441292.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rsjprA9Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_9017.jpg) 所以，没有机会获得旧固件。我开始准备实施我的 Atmega & HM-10 团队。我把 HM-10 挂在塞拉斯的机器人(我儿子的机器人)的背包面包板上。

我开始对等级转换的问题感到非常沮丧。我试过 [CD4050](http://www.fairchildsemi.com/ds/CD/CD4049UBC.pdf) ，但发现它是单向的，这意味着我仍然需要一个用于 Rx 总线的转换器(HM-10 和 Arduino)，或者，每次我想将草图上传到 Arduino 时，从 Rx 总线上拔下 HM-10。最后，我开始这样做，并为 Tx 线使用分压器。

这时候我又碰到了另一个问题:**范围**。

更确切地说，是射程不够。如果距离超过 7 英寸，小模块就会失去连接。唉。

回到故障排除。我无法确切指出问题所在。我确实发现这些模块有一个功率设置(AT+POWEx，X=0-4)。但是无论我把模块放在什么设置上，它们都没有超过 7 英寸的范围。但我确实注意到，当我移动从设备时，我可以通过将主设备对准从设备来获得连接。但是如果我旋转模块，它会失去连接。我不想做，但我开始阅读遥测技术。

我不能说我学到了什么，虽然，我确实发展了一个理论。我放在分线板上的接地层干扰了遥测技术。

第二次，我打开了热风枪，把 HM-10 从分线板上拉了下来。我回到 Eagle，重新设计了突破，去掉了下面的接地层

**[HM-10 Breakout v9.3](http://www.ubermentis.com/files/BLE_v9_3.rar) (未测试)**

我想，只要我要再次印刷电路板，我就会继续下去，并添加某种级别的转换。我评测了几款不同的 SMD 芯片(CD4050，74LVC245，TXB0108 等。)但是我发现芯片不是单向的就是定价过高。最终，我决定采用与 [Ada 的](http://www.adafruit.com/products/757#Downloads)和 [Spark 的](https://www.sparkfun.com/products/8745) 4 通道 I2C 转换器相同的[设计](http://www.adafruit.com/datasheets/AN10441.pdf)。

这种设计便宜、可扩展，并且需要很少的布局空间。这相当简单，从高到低的分压器，以及低到高的一个棘手的小 N 沟道 MOSFET。低到高电路实际上是双向的(达到一定速度)，但我只是用它来提高从 HM-10 到 Arduino 的 Tx 电压，同时保护 HM-10 不上传草图。

[![](img/a476728287fbdbfa22a60fb7b99d17d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jYFEqC_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Logic_Converter_Schem.jpg)

而且，目前为止就是这样...抱歉。

我已经拿到了学士学位，应该会在周一拿到新的考试成绩。

**LED 和散热器**

[![](img/1469222ad2531f34cdfe6ea90c0c10ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x0pI0g7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0589.jpg)

不久前，我意识到我需要开始更好地记录，并认为一张照片值得一千个字，所以在 32fps x 1,000 的情况下，嗯，在短视频文件应该踢屁股(我提交进一步的证据 [chickenparmi](http://www.youtube.com/user/chickenparmi) 的作品:)。好吧，我开始想如何制作这些视频。那时候我就有了天花板上挂一块木头的想法——随便抄这个设计，是开放式的——设计。

[![](img/efc4fb911ae2870f7c5c759a2ea09496.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z-_pp0II--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8868_513x768.jpg)

嗯，我在上面贴了一块有孔的板，这样我的 iPhone 就可以放在那里，拍下我的手在做什么。但我注意到，在黑客时间(妻子睡着后)，光线不足，无法拍摄合适的视频。从那时起，我开始研究廉价的大功率发光二极管。它们并不太难处理，我最终需要的是。

*   [PSU](http://www.ebay.com/itm/12V-Regulated-Switching-Power-Supply-2-95A-35W-AC-DC-PSU-/111117029499?pt=US_Radio_Comm_Device_Power_Supplies&hash=item19df17507b) ~$6
*   [LED](http://www.ebay.com/itm/110943602647?ru=http%3A%2F%2Fwww.ebay.com%2Fsch%2Fi.html%3F_from%3DR40%26_sacat%3D0%26_nkw%3D110943602647%26_rdc%3D1)4.15 美元
*   [散热片贴](http://www.fasttech.com/products/1049304) $6.15(不过，评论说[这个](http://www.fasttech.com/p/1391700)更好)
*   [散热器](http://www.fasttech.com/p/1173705) $5.48

**总计约 21.48 美元**

这可能看起来很高，但散热膏被用于许多其他事情；我做了 3 个其他的 LED 轨道灯，和其他的 LED 照明项目，我大概用了 5%的灯管。PSU 还兼作工作台电源:)

和许多其他项目一样，这个项目还没有完成。最初的计划是添加一组光传感器，在我移动双手时调节光线，从而自动校正视频中的光线不平衡。那部分还没完成。

但是我跳向前。当我第一次开始这个小项目时，我不知道如何使用大功率 led。我那小小的无知的自我以为它们就像 20ma 的发光二极管，对吗？我很快发现热位移是最重要的问题。这就是为什么我在订购散热器和粘贴之前 3 周就订购了 800 流明的 LED。

[![](img/4f4571abe2f0e4fdd7d879651a8d09b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kr6HMJFk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8829_2.jpg)

然后，它来的问题，找出我的散热是足够的大规模的 LED。(我认为 60 瓦的钨丝也能产生 800 流明？我知道他们会做小蛋糕-什么？那是我姐姐的简易烤箱，不是我的。)我跑题了，所以作为[计算障碍](https://dopasolution.com/dyscalculia/)我试图在不钻研高等数学的情况下找出我是否散热正常。这时候我想起我有我的[热电偶](http://www.adafruit.com/products/269)一起从我建立的咖啡烘焙。我将耦合器与 Arduino 和 i2c LCD 连接在一起，使我获得了一个非常精确和鲁棒的温度传感器。

我找了一下，但找不到 800lm LED 的热故障信息。最终，我想我应该用遮蔽胶带将耦合器探针贴在 LED 的表面，并将其连接起来(当然是用一个[合适的电阻器](http://led.linear1.org/1led.wiz))。

LED 灯亮了 1.5 秒，然后温度升到 180 华氏度左右。嗯，是的，我把散热器弄坏了。这一次，我用一点导热膏将 LED 固定在散热片上。我认为附上两个螺丝，这进一步对散热片施压的 LED。然后，我把耦合器探头对着 LED 的表面。我咬掉最后一个指甲，重新打开 LED 灯。这一次，温度传感器上升的速度*比*慢得多。几分钟后停留在华氏 112 度左右。当然，我不知道这是否超出了热崩溃的点，但我认为既然我自己的体温不是很高，我也没有爆炸，那么它可能是好的。我还触摸了 LED 的表面，并能够让我的手指离开超过 30 秒。我发现这是经验上最可靠的测试。随着越来越多的证据，我决定在我的天花板上再开一个洞...东西和附加灯。现在我们有了。当我添加了光传感器阵列后我会报告的。

[![](img/69e203a21d6da9347082176c3a54c617.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--sNlRy_3W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0593.jpg) [ ![](img/9e2c8651b7ce031f0adf2f5879019265.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qRZgCtdd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0592.jpg)

**XL4432 突围** - **遥测是巫毒**

[![](img/d1aa4f2e1591cd15e39feee03c7a9cba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wCbKEuuK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/XL4432.jpg)

当我在阅读遥测技术时，我发现了这些价格为 3.98 美元的小板子，并开始好奇除了砌砖之外，我还能做什么。我对“1000 米”范围的说法非常好奇即使 BS de-modifier 将范围降低到 500 米左右，这仍然是我喜欢的东西。所以我订了两个，为他们做了分线板。

他们一进来，我就有一种毛骨悚然的感觉，这让我想起了 HM-10 主板。好吧，我和他们玩得还不够多，不知道我是否杀了他们。我的逻辑电平转换器打滑，给电路板提供 5v 电压，这让我很恼火(一根便宜的跳线从试验板上松脱了)。

我停下手头的工作，重新制作了分线板，加入了一个[TXB 0108](http://www.ti.com/product/txb0108)([Digi-Key](http://www.digikey.com/scripts/dksearch/dksus.dll?pv1291=14&FV=1c0002&k=TXB0108&mnonly=0&newproducts=0&ColumnSort=0&page=1&quantity=0&ptm=0&fid=0&pageSize=25):2.30 美元)。这与 Ada 的 [8 通道双向](http://www.adafruit.com/products/395)逻辑电平移位器中的小芯片相同。

[**XL4432 突围**](http://www.ubermentis.com/files/XL4432_Breakout.rar) 鹰档 **(** **尚未测试)**

[![](img/98868c1495817544a01aca0f4b14d233.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wLT9vRcK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/677px-224049mvmueuee2ise9mu3.png)

这真的是整个故事，除了我一直在试图寻找关于用 Arduinos 连接这些小家伙的信息，以便我用电压转换器连接它们。到目前为止我已经找到了很多，但是这个看起来很有希望。可悲的是，我不明白他是如何通过他糟糕的原理图把 XL4432 连接起来的(？).有人想说说这些理由是否有关联吗？逻辑和 CtC 都声明，“总是，*总是*，连接所有地。”如果我记得我的 EE 示意图术语，当有一个[非常节点](http://books.google.com/books?id=vJk_d2dxO30C&pg=PA40&lpg=PA40&dq=extraordinary+node&source=bl&ots=4H9vAKxvns&sig=VAb-yba-SUZpoUkeGy9OLhwhgyY&hl=en&sa=X&ei=kibzUZv9FYmK9gSi14CoAg&ved=0CDUQ6AEwAQ#v=onepage&q=extraordinary%20node&f=false)时，dot on 连接不是最常用的吗？

哦好吧。

**Atmega 保险丝医生&弹簧板**

[![](img/ffc29fc94a6ce7b2a13e237d1ba28b4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RZ8A9JyV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Board_Layout.jpg)

我没有耐心。一点也不。这让我想到了几块 Atmega 板( [1](http://letsmakerobots.com/node/36273) 、 [2](http://letsmakerobots.com/node/36763) )。这让我很不爽，尤其是其中一个筹码是 12-17 美元。我曾在 Atmel Studio 中用砖砌芯片试图设置它们的保险丝。由于这些芯片的价格，我觉得我开始寻找解决方案是很自然的。显然[导火线医生](http://mdiy.pl/atmega-fusebit-doctor-hvpp/?lang=en)就是这样一个解决方案。本质上，它使用了 Atmel 芯片内置的高压编程功能。

我想，“太好了。拯救我筹码的方法！”但是...当我看到电路板制作时，我遇到了一个问题，这是一个家庭设计的蚀刻。我仍然没有没有氯化铁污渍裤子。所以，我开始重新设计我可以寄给 OSHPark 的版本。

我发现设计师已经准备好了电路板的 SMD 版本。但是，看完之后，我得出的结论是，打印起来太贵了。所以，我开始缩小它。

[**重制贴片原理图**](http://www.ubermentis.com/files/Brick_Doc_02.pdf)

[![](img/5bbba5ece31c9a323869ca05e1ad93d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JEWo1Wyf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8980.jpg)

最后，我花了大约 12 美元打印了一块板。但是像这里的其他项目一样，它没有像预期的那样工作。这个问题与我错过的一个额外节点有关，它导致了稳压器周围的短路。所以，我只是对轨迹进行了切片，至少能够在 Atmel Studio 中调出，并将十六进制文件写入芯片。因此，从理论上讲，如果我能纠正短路，给它提供 12v 电压，并将其连接到 Atmel 芯片，我应该能够恢复它们。

[![](img/0d0e9562b732a93b6d7682cef1cd9249.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Qx13uLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0054.jpg)

**T2】**

您可能会在这张图中看到，我通过一个过孔向电路板提供预调节 5v 电压。这是为了避免车载调节器周围的短路。此外，我必须将 AVR 线连接到 1k 电阻的另一端，以便在 Atmel studio 中读取电路板签名。

下面是材料: **[BOM](http://www.ubermentis.com/files/Atmega_Fuse_Doc_BOM.pdf)** 。

Kari loy——顺便说一下，他有一个完整的[保险丝医生](http://letsmakerobots.com/node/35100)——提醒我，即使我完成了我的板，我可能也不应该直接把它挂在砖板上。相反，我需要直接连接到芯片上。当然，在最初的保险丝医生中，这是通过 DIP 插座完成的。但是我不用蘸酱...所以，我开始想我应该放弃这个想法。

然后，我想起了[弹簧针](http://en.wikipedia.org/wiki/Pogo_pin)。我跳到易贝身上，看看我是否能找到任何足够小的引脚，以适应我所用芯片上的 TFQP 引脚。这些是我最后订购的[别针](http://www.ebay.com/itm/160926992225?ru=http%3A%2F%2Fwww.ebay.com%2Fsch%2Fi.html%3F_from%3DR40%26_sacat%3D0%26_nkw%3D160926992225%26_rdc%3D1)。

[![OddBot's Drill Press](img/cc533a381ef83b8e6014c93628b2f614.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xxJ0bE71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_1655.jpg)

当它们进来的时候，我很高兴地发现它们可能足够小，可以在不接触到邻居的情况下对付一根导线。然后，我开始寻找一种方法来创建一个夹具。我决定使用我剩下的 HDPE(切菜板)。我想我可以用 OddBot 的旧钻床，用亨特·S·汤普森董事会失败后剩下的钻头在上面钻孔。

当我准备好钻孔时，我进入 Eagle 并打印出 32-TFQP 芯片的足迹。我把它切掉，然后把我的 HDPE 切成同样的大小，做成两块，中间有足够的空间放钻子。然后，我在相邻的角上钻了两个洞。我放了几个 4-40 的螺丝和螺母把两块高密度聚乙烯固定在一起。想法是，我可以稍后在它们之间放置垫片，将弹簧针从顶部穿过，将导线焊接在其底部，然后让它们停留在 HDPE 的底部。不知道我为什么要说这些....我觉得图片解释了。

[![](img/ca72be06de36028fb64cbb85ed11a87d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Mg36dYu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0642.jpg)

在我把洞的东西拧上、敲上、敲紧之后，我跑到 OddBot 的钻床旁，用我所有的最小的钻头给它装上，然后在其中一个垫子上敲了几下。然后我拔出一个小别针，惊讶地发现它非常合适。

这就是我停下来的地方，主要原因是不想在 Atmega-328-P 上查找高压串行编程接口的引脚。

**恒温器**

[![](img/1fb1fe40bc54c29588da7ef0fb6f31a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UtInFG3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0663.jpg) 我不会写这篇文章的，除非我做了和我偷它的那个家伙不一样的事情。

不久前，我的妻子 Bek 让我给她做一个智能恒温器，我回答说，“我只达到了智能恒温器的水平。那行吗？”她怒目而视，所以我谷歌了一些飞机。

我找到了这个人的 Arduino 恒温器，我意识到我已经找到了大部分零件。

- Arduino Uno

- I2C 实时时钟

-- I2C LCD

-seed 继电器屏蔽

-达拉斯 1 线温度( DS18B20)

[![](img/cbdbc2568379984cca3b3e227342a5b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hyD9GrkK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0653.jpg)

这个想法非常简单，电压线在继电器屏蔽的所有腿上绑在一起，Arduino 连接到它。Arduino 从传感器读取温度，检查 I2C RTC 上的时间，并将二者打印到 I2C LCD 上。此外，如果时间是正确的(下午 4:00 后)，它会自动启动空调。我已经得到了所有的一起和工作，只是没有连接到交流线路。我试图找到一种方法来供电，因为所有的交流线路都是 24v，没有接地。所以，我买了:

*   PSU(笔记本电脑):10.75 美元
*   [绳索](http://www.ebay.com/itm/Brand-New-3-Prong-Port-Laptop-US-AC-Power-Adapter-Cord-Cable-Hot-Sale-SH/171086070732?_trksid=p2047675.m1850&_trkparms=aid%3D333005%26algo%3DRIC.FIT%26ao%3D1%26asc%3D12%26meid%3D145761556553529940%26pid%3D100011%26prg%3D1005%26rk%3D3%26rkt%3D5%26sd%3D181167704446%26):1.65 美元
*   DC-DC 转换器:1.96 美元

也就是说，6A 未调节时为 15v，1.6a 时为 4.5-40v

现在回过头来看，**这个项目比买智能恒温器还贵，我不推荐。**我选择这一版本的主要原因是，除了温度传感器和 PSU，我拥有一切。

**树莓 Pi 上的 Distcc 和 Gstreamer:**

当我开始玩 OpenCV 时，我爱上了它。我只是觉得它为我们提供了这么多，但我真的把我的修补限制在树莓派上。当我完成 Pi 上的小 [**面部追踪器**](http://letsmakerobots.com/node/36947) 时，我知道它作为传感器会很好，但不会接近我想要的功能水平。简而言之，Pi 根本不能给我所需要的速度。我在桌面上打开 OpenCV(我不会详细说明，只知道它比 Pi 快)，然后很快意识到，如果我想得到我想要的结果，我需要将视频数据从 Pi 发送到桌面。

因此，我开始尝试用廉价的方式将视频数据发送到桌面。我首先尝试使用 [Gstreamer](http://gstreamer.freedesktop.org/) 将视频数据从 Pi 通过 WiFi 传输到桌面上的 OpenCV。二十小时后...我意识到我太笨了，不能让这一切发生。我开始阅读。

简而言之，这个过程有太多的兼容性问题。我仍然认为这是可能的，我只是努力想弄明白。据我所知，并不是所有 Gstreamer 的开发插件都能在 Pi 上工作。接下来的问题是什么将会捕获视频(Motion、FFMPEG 等)，以及这些程序中是否有一个(如果有的话)喜欢使用 OpenCV，更不用说来自 Raspberry Pi 的视频管道了。没有必要说，但我会反正，这是一个烂摊子。

我在 Pi 上构建 Gstreamer 和 FFMPEG 的次数已经数不清了(不多，我数到大概 5 次)。这让我想到，“如果我能在桌面上编译这些强大的程序，速度可能会更快。在对交叉编译文献进行了一番挖掘之后，我决定使用 [Distcc](https://code.google.com/p/distcc/) 。它看起来很漂亮。这是一个真正的远程编译器，这意味着没有 SD 卡交换和安装，卸载。让它在 Pi 上运行才是关键。

我不会浪费所有的时间和精力去整理如何设置 Distcc 而且，就像这里描述的其他项目一样，我还没有完成。不过，这家伙的[帖子](http://jeremy-nicola.info/portfolio-item/cross-compilation-distributed-compilation-for-the-raspberry-pi/)帮了我大忙。我学习了很多关于 Bash 脚本的知识，并编写了一个脚本来设置 Distcc，请注意，**这个脚本还不能工作，**就像这篇文章的其余部分一样，不完整:

# [T1】！/bin/bash](#binbash)

# 脚本名-脚本的描述

# 文本颜色变量

```
txtund=$(tput sgr 0 1)          # Underline
txtbld=$(tput bold)             # Bold
bldred=${txtbld}$(tput setaf 1) #  red
bldblu=${txtbld}$(tput setaf 4) #  blue
bldwht=${txtbld}$(tput setaf 7) #  white
txtrst=$(tput sgr0)             # Reset
info=${bldwht}${txtrst}        # Feedback
pass=${bldblu}${txtrst}
warn=${bldred}${txtrst}
ques=${bldblu}${txtrst}
#Sample
#echo "$bldred How are you today? $txtrst"
sudo apt-get upgrade -y
echo"$bldred 16% $txtrst"
sudo apt-get update -y
echo"$bldred 32% $txtrst"
wget https://toolbox-of-eric.googlecode.com/files/libiberty.tar.gz
sudo tar -xvf libiberty.tar.gz
cd /home/pi/libiberty
sudo ./configure --enable-install-libiberty
sudo make
sudo make install
echo"$bldred 48% $txtrst"
cd
cd /home/pi
sudo apt-get install cmake -y
echo"$bldred 64% $txtrst"
sudo apt-get install subversion autoconf automake python python-dev -y
echo"$bldred 80% $txtrst"
cd
cd /home/pi
pwd
echo # The remote machines that will build things for you. Don't put the ip of the Pi unless >>.bashrc
echo # you want the Pi to take part to the build process. >>.bashrc
echo # The syntax is : "IP_ADDRESS/NUMBER_OF_JOBS IP_ADDRESS/NUMBER_OF_JOBS" etc... >>.bashrc
echo # The documentation states that your should set the number of jobs per machine to >>.bashrc
echo # its number of processors. I advise you to set it to twice as much. See why in the test paragraph. >>.bashrc
echo # For example: >>.bashrc
echo export DISTCC_HOSTS="192.168.0.102/16" >>.bashrc
echo  >>.bashrc
echo # When a job fails, distcc backs off the machine that failed for some time. >>.bashrc
echo # We want distcc to retry immediately >>.bashrc
echo export DISTCC_BACKOFF_PERIOD=0 >>.bashrc
echo  >>.bashrc  
echo # Time, in seconds, before distcc throws a DISTCC_IO_TIMEOUT error and tries to build the file >>.bashrc
echo # locally ( default hardcoded to 300 in version prior to 3.2 ) >>.bashrc
echo export DISTCC_IO_TIMEOUT=3000 >>.bashrc
echo # Don't try to build the file locally when a remote job failed >>.bashrc
echo export DISTCC_SKIP_LOCAL_RETRY=1 >>.bashrc
echo  >>.bashrc
sudo git clone --depth=1 git://code.opencv.org/opencv.git
cd opencv
sudo mkdir redist && cd redist
sudo apt-get update -y
sudo apt-get install distcc -y
sudo apt-get update -y
echo"$bldred 100% $txtrst" 
```

Enter fullscreen mode Exit fullscreen mode

我不好意思说我花了多少时间试图让 Distcc 在 Pi 上正常工作。浪费了这么多时间都是我的错。最后是[手动](http://distcc.googlecode.com/svn/trunk/doc/web/man/distcc_1.html)救了我。

因为我没有真正理解这个场景中的符号链接，所以我很难理解 Distcc 如何调用正确的编译器。根据我的推断，创建了一个符号链接来代替 gcc，c++，cpp，cc，g++。然后，当其中任何一个被调用时，符号链接会将数据重定向到桌面上的远程编译器。因此，当我第一次安装 Distcc 时，因为$PATH 变量不正确，我想，“好吧，如果它创建了到另一个编译器的符号链接，我应该删除 Pi 上的本地编译器——反正它们是不需要的。这样我肯定能拿到遥控器。”由于这个糟糕的逻辑，我发出了这个命令

**mv local . compilers local compilers . old**

可悲的是，直到我读了手册(嘿，这是一本很长的该死的手册)，我才发现“在数据被发送到远程编译器之前，使用了本地预编译器。”也就是说，每当我禁用本地编译器时，我就破坏了它。

[![](img/c3d6860657041927a1db967941d3202a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0HDd3s8d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Distcc_3_2.jpg)

如果 Distcc 的符号链接首先出现在$PATH 中，它将调用它作为预编译器，然后将它从$PATH 变量中删除。Distcc 然后调用$PATH 变量中的下一个编译器，这一次，它应该是远程编译器。

假设我移除了局部变量，第一个编译器会发现它被当作预编译器，然后移除它，留下没有编译器做真正的编译。

这导致我得到错误，说没有找到编译器。

我是在等待我的一位客户完成心理健康检查时发现这一切的。当我用头撞墙说，“狗娘养的...”时，他肯定很困惑。

我一直在翻手机手册。

总而言之，我现在知道真正的编译器和 distcc 的符号链接都必须在$PATH 变量中，它们只需要顺序正确。

但正如这里所有悲伤的故事一样，它还没有结束。**