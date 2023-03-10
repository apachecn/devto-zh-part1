# Arduino 至 RPi -电流隔离 I2C

> 原文：<https://dev.to/ladvien/arduino-to-rpi-galvanically-isolated-i2c-3lm0>

[分线板](///files/ADUM1250ARZ_v.01.zip)
[分线板](///files/ADUM1250ARZ_v.01.zip)

[https://www.youtube.com/embed/P77ZGNKlc9M](https://www.youtube.com/embed/P77ZGNKlc9M)

我已经等了很久才把我的树莓派集成到我的机器人里。但由于我对电知之甚少，我对自己发誓不会把圆周率加到我的机器人上，直到我完全确定不会把它烤焦。

嗯，我仍然不是“绝对”确定，但我觉得这个小光隔离器让我更接近了。这是建立在大约一周前我关于制造老鹰零件的帖子上的。

我计划实际列出一个喘息的图像需要什么样的调整才能让这个光隔离器工作。这其实很简单——但无论如何，不要被 quick2wire 所诱惑。那些混蛋浪费了我大部分的时间

如果有人有问题，请告诉我。

哦，还有一个问题。当我填充电路板时，我在 Arduino 端使用了 4.7k 电阻，但在 Raspberry Pi 端我去掉了所有电阻。似乎 Pi 内置的上拉可以很好地完成这项工作。

[ADUM1250ARZ 数据手册](http://www.analog.com/static/imported-files/data_sheets/ADUM1250_1251.pdf)

希望大家都好:)