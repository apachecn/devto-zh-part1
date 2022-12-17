# 在 Raspberry Pi 上安装 Node.js

> 原文：<https://dev.to/adamkdean/installing-node-js-on-a-raspberry-pi-4p0>

Raspbian 是基于 Debian Wheezy 的，所以与标准的 Ubuntu 14.04 安装有些不同。请记住，在您将脚本卷曲到 bash 之前，一定要阅读它。

```
curl -sLS https://apt.adafruit.com/add | sudo bash
sudo apt-get install node 
```

Enter fullscreen mode Exit fullscreen mode

现在检查它运行正常...

```
pi@raspberrypi ~ $ node -v
v0.12.0 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这是一个有点落后，但仍在 0.12.X。