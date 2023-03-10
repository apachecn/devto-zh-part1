# Raspberry Pi Zero 作为短信网关

> 原文：<https://dev.to/joenas/raspberry-pi-zero-as-sms-gateway-58gk>

[![Raspberry Pi Zero as SMS gateway](img/09c5f334bb52b9ea5f774bf197e0c158.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FWDTCqdl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/IMG_7391_crop.JPG)

我以前写过关于使用 3G 调制解调器设置短信网关的文章。我把它插在我的 HTPC 上，老实说，它放在电视旁边不好看。我觉得用树莓酱把它藏起来会更好。与此同时，我正准备创建一个[魔镜](https://magicmirror.builders)(没有实际的镜子)和我偶然(？)在我定期逛街买圆周率的东西时，偶然发现了 [Adafruit Fona 808](https://www.adafruit.com/product/2542) 。

读了一些之后——有很多很棒的教程我会在文章的最后链接到——我觉得应该有一些更简单的东西，我找到了一些为 Pi 设计的帽子。

我最后从 six fab 买了一个，但是这些 T4 中的任何一个都应该可以。(如果你尝试其中的一种，我很乐意听到它！)

[![Raspberry Pi Zero as SMS gateway](img/7d4c6a7a22d5d24ee202d4b21b16cd5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3qmUFi6y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/GPRS_1.5_New-1024x1024-1.jpg)

### 组装

我有一个烙铁，但除非必要，否则我不会使用它，所以想象一下当我发现[无焊 GPIO 接头](https://thepihut.com/products/gpio-hammer-header-solderless)时的喜悦。同时我还买了一个漂亮的小[盒子](https://thepihut.com/products/zebra-zero-for-raspberry-pi-zero-black-ice)。组装标题和情况是相当容易的，结果很好！就按照[这个指南](https://learn.pimoroni.com/tutorial/sandyj/fitting-hammer-headers)去做吧。

[![Raspberry Pi Zero as SMS gateway](img/90d01c95754cabd39f330bfb3398a29a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YmwqArK5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/IMG_7386-1.JPG)

*零点安装在钻机上*

[![Raspberry Pi Zero as SMS gateway](img/ac2e1d6b9dcde526518e985e871fce63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n4ZzCEGa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/IMG_7387-1.JPG)

*附表头*

[![Raspberry Pi Zero as SMS gateway](img/287ce53fb97560a60a88dc45c0888f4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vwt60S3U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/IMG_7388-1.JPG)

*组装好的箱子*

[![Raspberry Pi Zero as SMS gateway](img/d49e9c29d4cc3d75633088dd2c82cf0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tecNf7qC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonnev.se/conteimg/2018/01/IMG_7391-2.JPG)

*最后 GSM pHAT 加了*

### 设置

现在开始工作吧！在 Sixfab 有一个[博客帖子](http://sixfab.com/updated-tutorial-2-make-a-ppp-internet-connection-with-sixfab-gprs-shield-on-raspberry-pi/)，但它旨在建立一个 GRPS 连接。我只是对用我已经安装的`gammu`发送和接收短信感兴趣(这次用`postgres`作为后端)。

首先，您需要禁用串行控制台。

```
sudo systemctl stop serial-getty@ttyAMA0.service
sudo systemctl disable serial-getty@ttyAMA0.service 
```

Enter fullscreen mode Exit fullscreen mode

这个对我很有效。根据您的 Pi，您可能需要这样做。

```
sudo systemctl stop serial-getty@ttyS0.service
sudo systemctl disable serial-getty@ttyS0.service 
```

Enter fullscreen mode Exit fullscreen mode

然后删除 boot 命令行中的相应行

```
sudo nano /boot/cmdline.txt
# Remove "console=serial0, 115200" or "console=ttyAMA0, 115200" 
```

Enter fullscreen mode Exit fullscreen mode

启用 UART 并禁用串行蓝牙。

```
sudo nano /boot/config.txt
# Add these
enable_uart=1
dtoverlay=pi3-disable-bt 
```

Enter fullscreen mode Exit fullscreen mode

禁用 BT 的最后一点对我来说很有用，我花了相当多的时间搜索才找到答案。我不使用 BT，但如果您使用，我记得在某处看到过解决方案...

### 连接

重启你的 Pi 并测试到 pHat 的连接，做这个

```
sudo nano /etc/gammurc
# Add 
[gammu]
device = /dev/serial0
connection = at115200 
```

Enter fullscreen mode Exit fullscreen mode

gammu 应该会找到您的设备

```
sudo gammu --identify 
```

Enter fullscreen mode Exit fullscreen mode

您也可以通过`screen`
与盾牌互动

```
sudo screen /dev/serial0 115200 
```

Enter fullscreen mode Exit fullscreen mode

键入`AT`，按回车键，你应该会得到`OK`。`Ctrl-A, D`退出。

如果一切正常，你现在应该可以使用`gammu-smsd`发送和接收短信。首先**按下加电键几秒钟，给防护罩**加电。

```
sudo nano /etc/gammu-smsdrc 
```

Enter fullscreen mode Exit fullscreen mode

这是我在 postgres 上的全部配置。

```
[gammu]
port = /dev/serial0
connection = at115200

# SMSD configuration, see gammu-smsdrc(5)
[smsd]
service = sql
driver = native_pgsql
host = localhost
user = smsd
password = SUPERSECRET
database = smsd
RunOnReceive = python /opt/gammu/receivesms.py 
```

Enter fullscreen mode Exit fullscreen mode

### 发送短信！

```
sudo service gammu-smsd restart
gammu-smsd-inject TEXT 123456 -unicode -text "Oh hai" 
```

Enter fullscreen mode Exit fullscreen mode

我还为`gammu`写了一个带有 API 后端的小 html 客户端，我(可能)会很快发布。

### 阅读清单

*   [Fona + Pi 零](https://github.com/InitialState/fona-pi-zero/wiki)
*   [Pi Wifi 网关](https://github.com/benstr/TUT-raspi-cellular-gateway/blob/master/readme.md)
*   [另一个 Fona](https://learn.adafruit.com/adafruit-fona-3g-cellular-gps-breakout/overview)
*   [SIM900](https://www.itead.cc/blog/raspberry-pi-sim900-gsmgprs-module-adapter-kit)
*   [AT 命令](https://garretlabs.wordpress.com/2014/06/05/raspberry-and-the-remote-controlled-relay-a-low-level-approach-a-k-a-at-modem-commands-the-usual-suspects/)
*   [更多关于串口和 BT](http://www.instructables.com/id/RASPBERRY-PI-3-and-RASPBIAN-JESSIE-GSM-3G-SERIAL-H/)