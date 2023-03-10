# 我的 Eagle PCB 演练

> 原文：<https://dev.to/ladvien/my-eagle-pcb-walkthrough-4f77>

## 第一步:

**访问 ADI 公司，注册一个** **[样本账户。我相信他们看起来很不错，每个月都会让你订购几个样品。](http://www.analog.com/en/content/samples/fca.html)**

## 第二步:

**从**[ADI 公司订购 ADXL345 芯片的几个样品。](http://www.analog.com/en/mems-sensors/mems-inertial-sensors/adxl345/products/product.html)

[![](img/bc21e3cdc700b72642959f3cdc443df9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4BAvepc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/samplerequest.jpg)

## 第三步:

**从 Sparkfun:** [ADXL345](http://www.sparkfun.com/datasheets/Sensors/Accelerometer/ADXL345-BreakoutBoard-v13.zip) 下载老鹰文件(注意价格)。

[![](img/eb8e55382f862f26289feceadd8bfac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SdPcZ02n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/adxl345sparkfun.jpg)

## 第四步:

注册一个 [OSHPark](http://www.oshpark.com/) 账户。然后，上传。在 Eagle 文件中找到的 brd。

[![](img/54f247cc1d3c2b1b71e985b996f2cfa4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4yMW4jam--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/OSHParksplash.jpg)

## 第五步:

**订购电容器。**

[2 个 0.1uF](http://www.ebay.com/itm/160318833833?ssPageName=STRK:MEWNX:IT&_trksid=p3984.m1439.l2649)

1 个 10uF

## 第六步:

试着在邮件人施展魔法的时候学习 Python。

[![](img/b55184bd2a26a91ba0021a00407f5c62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XmZA1zTh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/3236811556_e470493eef.jpg)

## 第七步:

翻开你的 Python 代码，收到邮件。

## 第八步:

把所有东西都拿出来。ADXL 分线板、ADXL345 芯片和电容。

## 第九步:

填充您的白板。在这一点上，一个好的熨斗会对你有好处。但一般来说，焊接 SMD 时，从最大的芯片开始。我们的例子是 ADXL345。在裸露的焊盘上涂上一些助焊剂。现在，拿一种非常细的焊料，比如 0.022，放一些在熨斗尖端。将焊料滴拖过 ADXL 将要放置的裸露焊盘。现在，珠子冷却后，在硬化的珠子上涂上助焊剂。想法是让芯片漂浮在这个通量上。

[![](img/a06ef84f485b7de84187615924c951e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gQmv_pq_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0543-Optimized_1200x900.jpg)

## 第十步:

**将 ADXL345 放在不可见的通量上，悬停在焊盘上方。确保芯片角上的小白点和下面的红点在同一个位置。**

[![](img/47c7f5ed15c1e7b3485085949d3558ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gnQYtoGq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0550_4000x3000_0.jpg)

## 第十一步:

把板子放在翻转过来的熨斗上。这是最重要的部分:**看芯片。**您希望看到的是，随着助焊剂从芯片下方流出，芯片神奇地漂浮到位，导致焊料珠与 ADXL345 裸露的铜焊接在一起。说真的，别看了:)。如果由于某种原因，你没有感觉到芯片在每个焊盘上都粘合好了，那么就轻轻地按下芯片的中间。我淡淡地说！

[![](img/45a238d0c8f2d45629178e9b345d0976.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--veqeQAg7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0547_4000x3000.jpg)

## 第十二步:

盖住 b .呃，装上电容器。**

[![](img/3316a6f661b2bf2edd4e7eac8b75c02e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nml40Chc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8697_1150x768.jpg)

[![](img/bfa1f93f36cdf75700e5960fee04b9b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--enjuUJQo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_8705_1150x768.jpg)

## 第十三步:

即插即用

## 第十四步:

意识到它不起作用是因为你生活糟糕。

## 第十五步:

将 ADXL 拉回来，用焊芯清洁焊盘，然后重试。

[![](img/617d3bbb779fb024e8b913fe97d8100b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMEA1ahc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0555_1024x768.jpg)

## 第十六步:

重复第 11 步，但这次，注意芯片，不要太认真。**

## 第十七步:

把它挂起来检查一下。* *芯片支持 SPI/I2C，但我更喜欢 I2C。所以，把那玩意儿接到 Arduino 上，看看它能不能工作。这个家伙提供的代码和关于连接的说明在代码顶部的注释中。

[adxl 345 I2C 的 Arduino 代码](https://github.com/jenschr/Arduino-libraries/blob/master/ADXL345/examples/ADXL345_no_library/BareBones_ADXL345.pde)

## 第十八步:

看着你的一元钱 ADXL345 变魔术。

[![](img/59d3ae8570354f3301fae640d383e1d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2dN-BKq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/ADXL345_Running.jpg)

## 第 19 步:

思考采样芯片、从 SparkFun 借用电路板布局、从中国购买低价电容器的道德问题；所有这些都能为你节省大约 12~25 美元，或者喝杯啤酒。

## 第二十步:

尽量不要滥用采样特权。

如果你有任何问题，我会尽我无知的最大努力。