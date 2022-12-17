# 超小型电机屏蔽罩(M^3)

> 原文：<https://dev.to/ladvien/mega-mini-motor-shield-m3-46bp>

[https://www.youtube.com/embed/Ny9dN20BRA4](https://www.youtube.com/embed/Ny9dN20BRA4)

我终于得到了我设计的超级迷你马达(M3)防护罩。我很惊讶，在填充板后:它真的工作了。该董事会是在制造了 Arduino Mega Mini 之后成立的。我注意到我不会真的减少我的[机器人](http://letsmakerobots.com/node/35922)的体积，因为将逻辑连接到我使用的 Arduino 电机驱动屏蔽需要大量的布线。因此，我开始设计一个可以直接插入 MegaMini 的电机驱动屏蔽。我拿出 Eagle 和各种 IC 的一些数据手册。

我开始使用 L298D 芯片，但很快对它在 MegaMini 上的设置方式感到失望。另外，反激二极管惹恼了我。我记得读过 [SN754410](http://www.ti.com/lit/ds/symlink/sn754410.pdf) 有内部 ESD 二极管。我开始研究芯片布局，得到了一个令我非常满意的电路板设计。

稍后我会尝试一个完整的记录；学习 html/css 让我精神疲惫(我知道，很容易。但是现在很多人都知道，从认知上来说，我就像盐上的蜗牛一样慢。)