# 今天就开始使用 Android 吧！

> 原文：<https://dev.to/mplacona/get-started-with-android-things-today>

Android Things 是一个非常流畅的 Android 操作系统，它帮助开发人员使用 Android 框架和工具构建物联网应用。你可以在[什么是真正的 Android 事物](https://androidthings.rocks/2017/01/01/what-really-is-android-things/)上了解更多信息。今天我们就来看看如何入门 Android 的东西。

## 我们的工具

这里描述的指令已经在 Mac 上测试过，但是应该可以很容易地与任何其他操作系统互换。

Android 开发者已经有了大部分必要的工具来开始使用 Android，但是这里有我们需要的。

*   [安卓工作室](https://developer.android.com/studio/index.html)
*   一个树莓派 3 -如果你身边没有树莓派，你可以在这里得到一个。
*   一个至少 8GB 的微型 SD 卡和一个适配器，以便您可以闪存它。我有[这个](http://amzn.to/2i0a4H9)，但是什么都可以。
*   Raspberry Pi 的[最新 Android things 预览图](https://developer.android.com/things/preview/download.html)副本。
*   [SD 卡格式器](https://www.sdcard.org/downloads/formatter_4/)
*   连接到路由器的以太网电缆。

## 闪烁的图像

Android Things 网站上有关于如何闪烁图片的说明，但是我发现这个问题很难理解，所以我做了以下的事情。

打开你用 Android things preview 下载的 zip 文件，里面应该有一个名为`iot_rpi3.img`的文件。如果你有文件，请随意跳过下一个标题。

### 但是我遇到了一个坎儿！

我在这里遇到了一个障碍，提取出来的文件叫做`androidthings_rpi3_devpreview_4.zip.cpgz`，提取后会得到一个叫做`androidthings_rpi3_devpreview_4 1.zip`的文件，这种情况会一直持续下去。我发现[这篇文章](http://osxdaily.com/2013/02/13/open-zip-cpgz-file/)部分有帮助。

其要点是，您需要通过终端解压缩文件，但是使用网站上描述的方法会返回如下内容:

```
Archive:  androidthings_rpi3_devpreview_4.zip
warning [androidthings_rpi3_devpreview_4.zip]:  76 extra bytes at beginning or within zipfile
  (attempting to process anyway)
error [androidthings_rpi3_devpreview_4.zip]:  reported length of central directory is
  -76 bytes too long (Atari STZip zipfile?  J.H.Holm ZIPSPLIT 1.1
  zipfile?).  Compensating...
   skipping: iot_rpi3.img            need PK compat. v4.5 (can do v2.1)

note:  didn't find end-of-central-dir signature at end of central dir.
  (please check that you have transferred or created the zipfile in the
  appropriate BINARY mode and that you have compiled UnZip properly) 
```

如您所见，我们的文件就在那里，但是仅仅使用`unzip`命令并不能提取它。7zip 还是能提取出来。您可以通过运行以下命令来安装它:

```
$ brew install p7zip
$ 7za x androidthings_rpi3_devpreview_4.zip 
```

### 格式化存储卡

将存储卡插入你的电脑，你可能会得到一条信息说:

> 这台计算机无法读取您插入的磁盘。

这意味着该磁盘已经安装了另一个映像。不要担心，只要点击忽略。

打开 SD 卡格式化程序，选择 SD 卡并点击格式化。

[![SD Card Formatter](img/f8890af0a8f6841ab68e3e2054c9e97d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f4YsM8r6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.androidthings.rocimg/SDFormatter.png)

在你的终端上运行`diskutil`并识别你的存储卡的磁盘号。

```
$ diskutil list
/dev/disk2 (external, physical):
#:                       TYPE NAME                    SIZE       IDENTIFIER
0:     FDisk_partition_scheme                        *8.1 GB     disk2
1:                 DOS_FAT_32 NO NAME                 8.1 GB     disk2s1 
```

卸载磁盘:

```
$ diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful 
```

我们已经准备好将 Android Things 映像闪存到磁盘。从运行`*.img`文件的目录中:

```
$ sudo dd bs=1m if=iot_rpi3.img of=/dev/rdisk2 
```

确保将`/dev/rdisk2`的值更改为运行`diskutil list`时得到的数字。因此，如果您使用了`disk4`，那么您应该使用`/dev/rdisk4`运行上面的命令。

这需要一些时间，所以去喝点咖啡吧。您正在将 4GB 以上的内存闪存到 SD 卡中，根据其速度，这可能需要 5 到 15 分钟。

当刷新完成时，您的计算机应该会提示您“您插入的磁盘无法被这台计算机读取”再次留言。我们完成了这部分。

### 启动图像

从电脑上取下存储卡，插入树莓 Pi 3。我更喜欢在这一点上把它连接到屏幕上，因为它给我信心，一切都很好。

把 Pi 的微型 usb 连接到你的电脑上，如果你把它连接到屏幕上，你应该会看到安卓系统启动了

[![Android Things first boot](img/eb1cfa34ce80fe3100ae220bbb4eadf6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EqHBl1K9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.androidthings.rocimg/android-things-boot.png)

太好了！但正如你在屏幕上看到的，我们没有连接，这意味着我们无法让亚行连接到它。因此我们无法将第一个应用程序上传到我们的设备上。文档的这一部分并不是非常清楚。

> 在刷新您的主板后，强烈建议将其连接到互联网。这允许您的设备发送崩溃报告和接收更新。

但我想这就是 IoT 中“我”的意思，对吗？

## 连接互联网

断开 Pi 与计算机的连接以关闭电源，并连接其上的以太网电缆。现在再次启动它，您应该会看到，这一次当它启动时，它被分配了一个 IP 地址。

[![Android Things first boot](img/92a57bd65ac0d752b949c248fb8d6319.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HEsLYRZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.androidthings.rocimg/android-things-ethernet.png)

回到您的终端，输入以下命令:

```
$ adb connect Android.local
connected to Android.local:5555 
```

`Android.local`由 Raspberry Pi 自动广播，并且应该解析为在端口 5555 上分配给 Pi 的 IP 地址，因此在我们的示例中，上面的命令与运行`adb connect 192.168.0.23:5555`相同。

在您的终端上运行`adb devices`现在应该会在设备列表中列出您的 Pi。但是一直连着一根线是一件痛苦的事情，所以让我们配置它来连接我们家的 WiFi。

### 连接到您的 WiFi

回到您的终端，输入以下内容:

```
$ adb shell am startservice \
    -n com.google.wifisetup/.WifiSetupService \
    -a WifiSetupService.Connect \
    -e ssid <Network_SSID> \
    -e passphrase <Network_Passcode> 
```

确保将`<Network_SSID>`替换为您的网络名称，将`<Network_Passcode>`替换为您的网络密码。从[了解到的树莓 Pi 3](http://raspberrypi.stackexchange.com/a/43311) ，它似乎只能连接到 2.4Ghz 网络，因为它只有一个 WiFi 天线。

运行上面的命令将使您的 Pi 连接到 WiFi。如果您仍然在屏幕上显示它，您将会看到，在您运行它之后，它会很快获得一个分配给它的 IP 地址。现在，您可以从其中移除以太网电缆。我发现重新启动它是最好的，因为它保证了设备不是`offline`。

## 运行您的第一个 Hello Things 应用程序

如果我们没有运行一个应用程序来确保 Android 确实如预期的那样在我们的 Raspberry Pi 上运行，这将不是一个完整的教程。您可以继续这样做，或者在这里克隆这个应用程序的库[。](https://github.com/mplacona/HelloThings)

在 Android Studio 中，创建一个名为`Hello Things`的新项目。Android Things 在 API 24 及以上版本上运行，所以确保您选择 Nougat 或以上版本，并以空活动开始。

打开你的应用程序级 Gradle 文件，添加一个对 Android 的依赖。

```
dependencies {
    ...
    provided 'com.google.android.things:androidthings:0.4-devpreview'
} 
```

现在转到应用程序的清单，向共享库添加一个条目，并修改意图过滤器。

```
<application
    android:label="@string/app_name">
    <uses-library android:name="com.google.android.things"/>
    <activity android:name=".MainActivity">
        <!-- Launch activity as default from Android Studio -->
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>

        <!-- Launch activity automatically on boot -->
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.IOT_LAUNCHER"/>
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
    </activity>
</application> 
```

最后，转到`hellothings/MainActivity.java`并更改`onCreate`方法，列出我们的 Raspberry Pi 上所有可用的外设。

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    PeripheralManagerService service = new PeripheralManagerService();
    Log.d(TAG, "Available GPIO: " + service.getGpioList());
} 
```

这将返回我们的 Raspberry Pi 中所有可用的通用输入/输出(GPIO)列表。

```
01-02 19:40:52.589 1636-1636/rocks.androidthings.hellothings D/MainActivity: Available GPIO: [BCM12, BCM13, BCM16, BCM17, BCM18, BCM19, BCM20, BCM21, BCM22, BCM23, BCM24, BCM25, BCM26, BCM27, BCM4, BCM5, BCM6] 
```

此外，如果您的 Pi 仍然连接到一个屏幕，您将看到名为`Hello Things`的活动已经启动。

[![Android Things application running](img/130e833be8ac6d1b0ccf1fff081bd484.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qXWAiuDl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.androidthings.rocimg/hello-things.png)

现在，继续在 [Android Things 网站](https://developer.android.com/things/sdk/samples.html)上玩一些其他的样品。