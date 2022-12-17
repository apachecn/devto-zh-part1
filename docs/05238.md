# 短信网关和推送通知

> 原文：<https://dev.to/joenas/sms-gateway-and-push-notifications-48ni>

今天的许多服务要么要求要么真的希望你在注册时提供一个手机号码。我不喜欢与这些服务分享我的私人号码，也不喜欢让这些短信塞满我的收件箱。

起初我以为我可以用 Twilio 来做这件事，但是显然他们不允许接收来自 Twitter 和脸书使用的短代码的文本。所以我决定建一个短信网关！你真的不需要太多来做这件事

*   3G/4G 调制解调器
*   sim 卡，最好是预付费的
*   一台电脑:)

如果你想要更高级的功能，有几个框架，例如 [playSMS](https://playsms.org) 或 [smsgateway](https://github.com/n0r1sk/smsgateway) 。我自己没有试过这些，但是它们看起来很棒！因为我真的只想让**收到**短信，所以我选择了 [gammu](https://wammu.eu/gammu/) 。我找到了这本[指南](https://andypi.co.uk/2017/05/06/raspberry-pi-sms-to-email-gateway-part-1/)，它解释了我必须做的大部分事情。

我家里放着一台旧的华为 E398。我按照下面的说明让它工作**，但几个小时后它会断开连接，我不得不重新插入**。我不确定这是由于调制解调器故障还是其他原因..所以 YMMV！

现在我用的是华为 E122 ，它在 OOTB 工作。`lsusb`为调制解调器给出

```
ID 12d1:1001 Huawei Technologies Co., Ltd. E169/E620/E800 HSDPA Modem 
```

Enter fullscreen mode Exit fullscreen mode

如果你有另一个调制解调器，可能值得检查一下[设备参考](http://www.draisberghof.de/usb_modeswitch/device_reference.txt)中的`usb_modeswitch`，如果它受支持的话。还有一个[参数参考](http://www.draisberghof.de/usb_modeswitch/parameter_reference.txt)给不喜欢`man-pages`的人。

## 切换 usb 模式

大多数调制解调器在你插入的时候会以 USB 存储的形式出现，主要是为了 Windows 用户可以安装驱动程序。所以你需要告诉它切换到调制解调器模式，为此我们使用`usb-modeswitch`。

## 华为 E398 使用说明

```
sudo apt-get install gammu usb-modeswitch 
```

Enter fullscreen mode Exit fullscreen mode

检查我们处于什么模式，因为我的调制解调器**存储模式**看起来像这样

```
lsusb
=> Bus 002 Device 019: ID 12d1:1505 Huawei Technologies Co., Ltd. E398 LTE/UMTS/GSM Modem/Networkcard 
```

Enter fullscreen mode Exit fullscreen mode

`12d1`上面是*厂商 ID* 和`1505`产品 ID 。

现在你需要找到**调制解调器模式**的产品 ID。一些指南建议插上调制解调器重启电脑，但这对我没有帮助，所以我不得不谷歌一下。有大量不同调制解调器的论坛帖子，我发现正确的 ID 是`1506`。

你还需要找到*“消息内容”*来发送带有`-M`标志的调制解调器。

切换调制解调器模式

```
# -v is current vendor ID
# -p is current product ID
# -V is target vendor ID (usually the same)
# -P is target product ID
# -M is the message
sudo usb_modeswitch -v 12d1 -p 1505 -V 12d1 -P 1506 -M 
"55534243123456780000000000000011062000000100000000000000000000" 
```

Enter fullscreen mode Exit fullscreen mode

检查调制解调器是否已切换模式

```
lsusb
=> Bus 002 Device 005: ID 12d1:1506 Huawei Technologies Co., Ltd. Modem/Networkcard 
```

Enter fullscreen mode Exit fullscreen mode

现在应该有一个或多个设备可以使用

```
dmesg | grep tty
# Should give something like
GSM modem (1-port) converter now attached to ttyUSB0
GSM modem (1-port) converter now attached to ttyUSB1
GSM modem (1-port) converter now attached to ttyUSB2 
```

Enter fullscreen mode Exit fullscreen mode

运行 gammu 配置并将`port`设置为`/dev/ttyUSB0`

```
sudo gammu-config 
```

Enter fullscreen mode Exit fullscreen mode

检查 gammu 是否能识别调制解调器

```
sudo gammu --identify
Device : /dev/ttyUSB0
Manufacturer : Huawei
Model : unknown (E398)
Firmware : xxx
IMEI : yyy
SIM IMSI : zzz 
```

Enter fullscreen mode Exit fullscreen mode

一切都好！现在你应该可以像这样发送短信了

```
sudo gammu sendsms TEXT [phonenumber] -text "Ohhai" 
```

Enter fullscreen mode Exit fullscreen mode

### 插入时或重启后自动切换模式

重启或拔下并重新插入调制解调器后，通常会再次切换模式。为了确保我们始终处于**调制解调器模式**，你需要为`usb_modeswitch`创建一个配置，匹配*供应商 ID* 和*产品 ID* 。

```
sudo nano /etc/usb_modeswitch.d/12d1:1505

DefaultVendor=0x12d1
DefaultProduct=0x1505
TargetVendor=0x12d1
TargetProduct=0x1506
MessageContent="55534243123456780000000000000011062000000100000000000000000000" 
```

Enter fullscreen mode Exit fullscreen mode

### 符号链接到/dev/sms 以确保一致性

为你的设备创建一个符号链接可能是个好主意，这样你对`gammu`的设置总是有效的，否则有时重启后它可能会切换到`/dev/ttyUSB1`或其他什么。

```
sudo nano /etc/udev/rules.d/999-sms-gateway.rules
# Add this line 
SUBSYSTEM=="tty", ATTRS{idVendor}=="12d1", ATTRS{idProduct}=="1506", SYMLINK+="sms"
# Then
sudo udevadm control --reload 
```

Enter fullscreen mode Exit fullscreen mode

## 用 WebHook 转发文本

我想将所有收到的文本发送到我的 [Huginn](https://github.com/huginn/huginn) 实例，并从那里发送到 [Pushover](https://pushover.net) 。Huginn 支持很多服务，所以如果你喜欢 Slack 甚至 Twitter，那也没问题！

我用的是这个脚本的修改版本[。](https://github.com/andy-pi/rpi-sms-gateway/blob/master/receivesms.py)

安装`gammu-smsd`并进行配置。

```
sudo apt-get install gammu-smsd
sudo pip install requests

sudo nano /etc/gammu-smsdrc
# Change the port in the [gammu] section to your device
# Add the RunOnReceive in the [smsd] section.
# RunOnReceive = python /opt/gammu/receivesms.py

sudo service gammu-smsd restart 
```

Enter fullscreen mode Exit fullscreen mode

您可以随意更改脚本的路径，我在其中放了`/opt/gammu`和`chown`:将目录添加到 gammu。

### 修改版本的脚本

```
#!/usr/bin/env python

from __future__ import print_function
import os, sys, re, requests

def get_message():
    files = [os.path.join('/var/spool/gammu/inbox/', m) for m in sys.argv[1:]]
    files.sort() # make sure we get the parts in the right order
    number = re.match(r'^IN\d+_\d+_\d+_(.*)_\d+\.txt', os.path.split(files[0])[1]).group(1)
    text = ''
    for f in files:
        text += open(f, 'r').read()
    try:
        text = text.decode('UTF-8', 'strict')
    except UnicodeDecodeError:
        text = text.decode('UTF-8', 'replace')
    return number, text

number, text = get_message()

request = requests.post('YOUR_HUGINN_URL', data={
    'from': number,
    'message': text
}) 
```

Enter fullscreen mode Exit fullscreen mode

要获得 Huginn 的 url，您需要做的就是创建一个 [Webhook 代理](https://github.com/huginn/huginn/wiki/Using-a-Webhook-Agent-with-external-software)。现在尝试发送一条短信到你的手机号码，它应该会出现在 Huginn！从那里你可以把它转发给你喜欢的任何服务，或者是内置的代理，或者是一个普通的带有[邮政代理](https://github.com/huginn/huginn/wiki/Agent-Types-&-Descriptions#post-agent)的 WebHook。

如果你有任何问题，给我发邮件或发微博。