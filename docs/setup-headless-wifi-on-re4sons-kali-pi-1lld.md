# 在 Re4son 的 Kali Pi 上设置无头 WiFi

> 原文：<https://dev.to/ladvien/setup-headless-wifi-on-re4sons-kali-pi-1lld>

我花 10 美元买了几个树莓派 Zero W。这是偶然的，我也购买了 Udemy 课程从零开始学习道德黑客。我想，我最好把这些东西放在一起。

*   [树莓派零度 W](https://www.raspberrypi.org/products/raspberry-pi-zero-w/)
*   [从头开始学习道德黑客](https://www.udemy.com/learn-ethical-hacking-from-scratch/)

我还发现了 Re4son 放在一起的粘手指 Kali Pi 内核和发行版。

*   [卡利皮](https://whitedome.com.au/re4son/sticky-fingers-kali-pi/)

到目前为止，它运行良好。然而，我还没有在定制内核上完全测试蓝牙 LE 硬件。

我遇到的一个问题是无法连接到新的热点。通常，您会引导连接到显示器、键盘、鼠标的 rp0w，并直接编辑 wpa_supplicant.conf。但是，如果您只想带着笔记本电脑和 rp0w 去一个新的地方，该怎么办呢？如果没有显示器等，您将如何向 rp0w 添加 wifi 凭据。

有一段时间，我试图让以太网小工具设置在 rp0w 上工作，但没有任何运气。我认为问题与试图在 Mac 上使用小工具硬件有关，而不是在 Windows 机器上。

最后，我决定添加一个脚本来完成以下任务:

1.  挂载/boot 分区(可通过 PC 的 SD 读卡器进行编辑)。
2.  在/boot 上查找名为“wpa_supplicant.txt”的文件，并将其复制到/etc/wpa_supplicant.conf
3.  在/boot 上查找名为“interfaces.txt”的文件，并将其复制到/etc/networks/interfaces
4.  卸载/引导
5.  删除/boot 目录

我在`/root`中将这个脚本保存为`wifi_setup.sh`。然后我在`/etc/rc.local`里给它加了一个调用

```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
/root/wifi_setup.sh || exit 1
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

这里是`wifi_setup.sh`

```
#!/bin/bash

if [ ! -d "/boot" ]; then echo 'Mounting /boot'
        cd ..
        mkdir /boot
        mount /dev/mmcblk0p1 /boot
fi

if [ -f "/boot/wpa_supplicant.txt" ]; then echo 'Applying wpa_supplicant'
        cp /boot/wpa_supplicant.txt /etc/wpa_supplicant.conf
        mv /boot/wpa_supplicant.txt /boot/wpa_supplicant.applied.txt
fi

if [ -f "/boot/interfaces.txt" ]; then echo 'Applying intefaces'
        cp /boot/interfaces.txt /etc/network/interfaces
        mv /boot/interfaces.txt /boot/interfaces.applied
fi umount /boot
rm -r /boot 
```

Enter fullscreen mode Exit fullscreen mode

这让我可以从我的笔记本电脑上添加一个新的网络，只需要一个 SD 读卡器。