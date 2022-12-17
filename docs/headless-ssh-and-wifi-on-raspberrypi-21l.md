# RaspberryPi 上的无头 ssh 和 Wifi

> 原文：<https://dev.to/kurzgedanke/headless-ssh-and-wifi-on-raspberrypi-21l>

#### 宋承宪:

要在没有显示器、键盘或鼠标的情况下在 RaspberryPi 上启用 SSH，请将您的 SD 卡放入读卡器并插入您的主 PC。

打开终端并导航至 SD 卡。

```
# On Mac:
╭─loki@lokiTheGod ~
╰─$ cd /Volumes
╭─loki@lokiTheGod /Volumes
╰─$ ls
BOOTCAMP MACINTOSH HD Untitled boot 
```

Enter fullscreen mode Exit fullscreen mode

现在您位于“宗卷”文件夹中，其中显示了连接到 mac 的所有驱动器。您必须在 Pi SD 卡上创建一个空的`ssh`文件。

```
╭─loki@lokiTheGod /Volumes
╰─$ cd boot
╭─loki@lokiTheGod /Volumes/boot
╰─$ touch ssh 
```

Enter fullscreen mode Exit fullscreen mode

输入`cd boot`进入 Pi SD 卡，然后输入`touch ssh`创建一个空的 ssh 文件。

您可以通过在终端中键入`ls`来验证这一点。

#### WiFi:

> 就我个人而言，这种方法不适合我…所以最简单的方法是通过以太网。

要在您的无头 Pi 上直接启用 WiFi，请在您的 Pi 的引导目录中放置一个文件名`wpa_supplicant.conf`。

`wpa_supplicant.conf`移动，同时 Pi 开始移动到 wpa 配置所在的`/etc/wpa_supplicant/wpa_supplicant.conf`。

一个简单且对大多数网络来说足够的`wpa_supplicant.conf`如下所示:

###### WPA:

```
network={
    ssid="YOUR_SSID"
    psk="YOUR_PASSWORD"
    key_mgmt=WPA-PSK
} 
```

Enter fullscreen mode Exit fullscreen mode

###### WPA2:

```
network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_NETWORK_PASSWORD"
    proto=RSN
    key_mgmt=WPA-PSK
    pairwise=CCMP
    auth_alg=OPEN
} 
```

Enter fullscreen mode Exit fullscreen mode

有关这方面的更多信息，您可以查看 Arch Wiki WPA 恳求者网站(链接)。

祝你和你的 Pi 玩得开心！