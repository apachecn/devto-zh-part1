# 在比格犬骨黑上恢复埃到 eMMC

> 原文：<https://dev.to/ladvien/restoring-angstrom-to-emmc-on-beaglebone-black-3phk>

[https://www.youtube.com/embed/-UuNNiwHWFU](https://www.youtube.com/embed/-UuNNiwHWFU)

更新:我发现我提到的链接(这是真实的库存图片)不可用，除非进行更新和升级。遗憾的是，2gb 的图像无法做到这一点。不管怎样，我已经将图像切换到更新后的(撰写本文时)Angstrom 图像。请仔细检查并确保您得到了最新的图像:

[http://beagleboard.org/latest-images](http://beagleboard.org/latest-images)

替换第 8 步和第 10 步中的路径(但我会尽量保持更新)。不幸的是，你需要一个 4gb 或更大的 microSD 来使用这些指令。

## 主:

正如我所说的，我在比格犬骨上杀死了我的股票埃布莱克(B^3).)

我拼凑出了修复它的方法。

你需要 B^3、microSD 卡和以太网连接。

(WiFi 加密狗可以取代以太网，如果你在我之前让它工作的话。如果你做了，在哪里演练:P？)

### 1。下载 Angstrom

**[埃](http://s3.armhf.com/boards/omap/bbb/bbb_angstrom_ga.img.xz)T3】**

这个家伙提供了几个 B^3 发行版，其中一个是股票 Angstrom。

### 2。下载并安装 7-Zip

[http://www.7-zip.org/](http://www.7-zip.org/)

### 3。下载图像编写器(Win32diskimager)

[http://sourceforge.net/projectss/win32diskimager/](http://sourceforge.net/projectss/win32diskimager/)

如果您使用的是 Linux，您可以尝试:

[https://help.ubuntu.com/community/Installation/FromImgFiles](https://help.ubuntu.com/community/Installation/FromImgFiles)

### 4。解压 bbb_angstrom_ga.img.xz 文件

[![](img/4f96198077cc90dd124eaf1dfcdb5c89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G38136s8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Unzip_with_7-Zip.jpg)

### 5。使用 Win32diskwriter 将 stock Angstrom 写入您的 microSD

1.  打开 Win32diskwriter，点击蓝色文件夹图标。
2.  选择你的 Angstrom**BBB _ Angstrom _ ga . img**文件。
3.  确保您的 microSD 是您的 PC，并且它已被选中(通常列为 H:)。
4.  点击**写入。**

[![](img/82309588dc028f6ff21d15dded9ecc04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7CbZLGYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/win32diskimager.jpg)

### 6。将 microSD 从您的 PC 中取出，放入 Beaglebone Black 中，并从其中启动。

**见视频**

### 7。使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (或者你最喜欢的 SSH 程序)访问 Beaglebone Black。

### 8。下载股票发行版

这是为了让我们可以把图像放在 eMMC 上，注意这一步你必须把 Beaglebone Black 连接到互联网上。

类型:

```
wget https://s3.amazonaws.com/angstrom/demo/beaglebone/Angstrom-Cloud9-IDE-GNOME-eglibc-ipk-v2012.12-beaglebone-2013.04.13.img.xz 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/06f3c2884fef5690d408ae12cccbdbb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DsdlQf9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Download_Stock_on_PuTTY.jpg)

### 9。把 Angstrom Stock img 写到你的 Beaglebone Black eMMC 上

下一步是将图像文件写入你的小猎犬的 eMMC。

注意，这需要一段时间，如果你想知道它是否还在安装，使用 LED 活动灯来引导你。当 PuTTY 窗口返回到命令提示符，并且 led 变慢时，就可以进入下一步了。哦，第二个音符。在此步骤中，尽量不要关闭小猎犬号。

类型:

```
xz -cd Angstrom-Cloud9-IDE-GNOME-eglibc-ipk-v2012.12-beaglebone-2013.04.13.img.xz > /dev/mmcblk1 
```

Enter fullscreen mode Exit fullscreen mode

### 10。关闭 Beaglebone 黑色

类型:

```
shutdown 
```

Enter fullscreen mode Exit fullscreen mode

### 11。移除 microSD 并重新给 Beagle 通电。

它现在应该像你买的那样启动了(当然，除非我搞砸了。你可以对我大喊大叫，我会修改这些说明)。**

如果你按照这个过程，你会看到这些指令可以用来写任何图像文件到 eMMC。如果你得到了一个不同的发行版，请告诉我。

现在，我将转向如何让 Arch Linux 运行起来。

希望你们都好。