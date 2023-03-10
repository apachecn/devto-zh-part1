# 树莓派和 Arduino 笔记

> 原文：<https://dev.to/adamkdean/notes-on-raspberry-pi-arduino-3bmn>

使用 NOOBS，安装 Raspbian。这可能是 Pi 最受支持的发行版。([https://www.raspberrypi.org/help/noobs-setup/](https://www.raspberrypi.org/help/noobs-setup/)

将 Raspberry Pi 设置为自动连接到 WiFi。我用过 TP-Link TL-WN321G，没有任何问题。([http://weworkweplay . com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/](http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/))

我用的是 2011 年以前的旧 Arduino Uno。它的固件版本实际上是 0.00，但我能够让它与 Firmata 一起工作。Firmata 是一个支持主机和 arduino 之间通信的库。它允许你使用 JavaScript 框架如[http://johnny-five.io/](http://johnny-five.io/)来控制你的 arduino 与 Node。

你只需要把 Firmata 放在 arduino 上一次，所以我在我的 MacBook 上这么做了。在这之后，Arduino 就开始准备通信了。不再需要编程。需要系绳，因为*主持人*现在将穿上裤子。

首先，下载 Arduino IDE。`brew cask update && brew cask install arduino`OS X。安装后，运行它，确保 arduino 通过 USB 连接。确保在 IDE 中选择了正确的板和端口。转到文件、示例、Firmata，然后转到标准 Firmata。把这个上传到你的板上。现在你准备好了。

让我们快速测试一下。使用节点，安装`johnny-five`。然后将 LED 插在 Arduino 引脚`13`和`GND`上。然后运行 hello world 闪烁代码:

```
var five = require("johnny-five"),
    board = new five.Board();

board.on("ready", function () {
    var led = new five.Led(13);
    led.blink(500);
}); 
```

Enter fullscreen mode Exit fullscreen mode

LED 应该闪烁。如果没有，是时候打开谷歌了。

接下来，我们想通过树莓派来控制 Arduino。为此，您需要正确管理您的功耗。

第一步。在连接 WiFi 加密狗的情况下打开 raspberrypi。等待它连接到网络。

第二步。开始连续 ping raspberrypi 以检查其连通性。

第三步。使用外部电源打开 arduino。
第四步
。将 USB 插头插入 raspberrypi。

第五步。将 USB 插头插入 arduino。

我不确定，但我认为通过先用外部电源给 arduino 通电，然后通过 USB 连接，它可以禁用 USB 功耗，这可以阻止你的 raspberrypi 发火。

下一篇文章将介绍:johnny-five arduino 代码/设置