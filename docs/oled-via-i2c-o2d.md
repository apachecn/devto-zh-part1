# 经由 I2C 到有机发光二极管

> 原文：<https://dev.to/ladvien/oled-via-i2c-o2d>

[https://www.youtube.com/embed/kNHjcDs1qfA](https://www.youtube.com/embed/kNHjcDs1qfA)

我猜这是一个 I2C 周末。我在 [Itead](http://imall.iteadstudio.com/im130625003.html) 工作室花了 5 美元找到了这些[有机发光二极管显示器](http://en.wikipedia.org/wiki/OLED)(有机发光二极管)图像  图像。

它们可能很小，但它们非凡的对比度和视角足以弥补这一点。另外，我是说，拜托，它们才 5 美元。我会说，他们在 3.3v 下工作让我有点恼火，我相信这意味着我很快就会为他们制作一个小小的电平转换器板。我估计转换板的价格在 1.25 美元左右，这是一个不错的价格

图像图像 OLEDs 的一些额外功能:图像

1.  图像更宽的视角(即，您不必直视它)。形象
2.  图像没有背光，使它们更平坦，耗电更少(不是少很多)。形象
3.  图像高刷新率。我唯一一次看到闪烁是通过我的摄像机。我有延迟(10)；在我的代码中:)图片
4.  图片它们很便宜(呃？).形象
5.  图片这就是未来:)图片

唯一让我吃惊的缺点是这些库大约有 9k 的 flash 上传。64x64 的 LMR 机器人大约在 1k 左右。

图片当我拿到它们的时候，我担心不深入研究[数据表](//ftp://imall.iteadstudio.com/Graphic_Display/IM130625003_128x64_OLED_Module/DS_SSD1306.pdf)就无法使用它们。但是后来发现，他们和 [Adafruit 的董事会](http://www.adafruit.com/products/326)是一个单位。对不起，我爱你，阿达...但是...付不起 19.50 美元。现在，也许如果[贝基·斯特恩](http://sternlab.org/)和他们一起来。呃。不管怎样，有了艾达出色的向导和软件，我在大约 10 分钟内就让 LMR 机器人四处移动了。所以，我最终会从艾达那里买些东西，然后用金钱来表达，“谢谢你，亲爱的。”形象

*   [Adafruit 关于使用图像的指南 SD1306 图像 128x64 有机发光二极管](http://learn.adafruit.com/monochrome-oled-breakouts/arduino-library-and-examples)
*   [Ada 的 Arduino 库，用于使用 SSD1306](https://github.com/adafruit/Adafruit_SSD1306) (它支持基本形状、字体和位图)
*   [LCD 助手](http://en.radzio.dxp.pl/bitmap_converter/)(用于将位图转换为字节数组)。

连接是这样的:

```
* imagesArduino Uno 3.3v <------------> OLED VCCimages
* imagesArduino Uno 3.3v <------------> LV of Logic Converterimages
* imagesArduino Uno 5v <------------> HV of Logic Converterimages
* imagesArduino Uno Gnd <------------------> OLED GNDimages
* imagesArduino Uno Gnd <------------------> LV Logic Converter GNDimages
* imagesArduino Uno Gnd <------------------> HV Logic Converter GNDimages
* imagesArduino Uno A5 (SCL) <---------  Channel 1 Logic Converter --------> OLED SCLimages
* imagesArduino Uno A4 (SDA) <---------  Channel 2 Logic Converter --------> OLED SDAimages
* imagesArduino Uno D4 (SCL) <---------  Channel 3 Logic Converter -------->  OLED RESETimages 
```

Enter fullscreen mode Exit fullscreen mode

我知道，我知道；我正在做一个转换器适配器，让所有的电线都消失。

[![](img/f8fe31ca9f29ccc21574aab73ca81345.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SZOVTCeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_9168.jpg)

我真的很喜欢这些小板子:)