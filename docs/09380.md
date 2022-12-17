# Lumi3 述职笔记

> 原文：<https://dev.to/ladvien/lumi3-debriefing-notes-4paj>

# C#学习日志:重构米露

## 米露上传者

自 2014 年 5 月以来，我一直致力于编写自己的 flash UART 上传程序。最初，我试图用 GCC 编译器用 C 写一个上传程序。想法是使用蓝牙 le 连接将针对 LPC1114 编译的 Intel HEX 文件远程上传到 uC。以下是为该项目设计的定制电路板的描述:

* [瓦尔迪兹变种人棋盘](https://ladvien.cimg/robots/valdez-mutant-board/)

不幸的是，这个项目超出了我的能力范围。在那里花了几个月的时间写 C 代码后，它就不能用了。当然，在这个过程中学到了很多关于 C 的知识。

几年后，我开始编写代码，使用瓦尔迪兹变种人文章中描述的相同方法上传编译好的 Atmel ATMega 和 ATtiny 程序。不过这次上传器是在 Windows 上用 C#写的。并且它与 Atmel uCs 上的 [TinySafeBootloader](https://ladvien.cimg/robots/tsb/) 接口。

奇怪的是，我居然完成了这个项目。第一个代码库是作为一个 [C#表单应用](https://msdn.microsoft.com/en-us/library/360kwx3z(v=vs.90).aspx)编写的。这工作做得很好！我实际上能够使用`System.Devices.Ports`来访问 CH340G 或 FTDI 芯片。然后，USB 转 UART 与 ATMega328P、ATtiny84 或 ATtiny85 上的引导加载程序握手(其他的也应该支持，但由于 Arduino HAL 的简单性，只对这些进行了测试)。

[![](img/1e1f33837c587289a7cf84f93dff0ea3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VSPHNmm7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/lumi_blink_upload2.png)

下面是代码库:

* [米露上传程序–Windows 窗体版本](https://github.com/Ladvien/Lumi_TinySafeBoot_Uploader)

当然，代码存在很多问题。大多数集中在编写面向对象代码缺乏经验。

以下是我发现的一些问题:

| 错误 |
| --- |
| 1.[上帝对象](http://source making . com/anti patterns/the-blob) |
| 2.[没有遵循 C#约定](https://msdn . Microsoft . com/en-us/library/ff 926074 . aspx) |
| 3.弃用(表单->通用) |
| 4.【同步 IO】(https://msdn . Microsoft . com/en-us/library/windows/desktop/aa 365683(v = vs . 85)。aspx) |
| 5.对 BLE 的支持率很低 |
| 6.糟糕的命名方案 |
| 7.代表/事件使用不当 |
| 8.对象抽象使用不当 |
| 9. |

事实上，正是由于缺乏蓝牙技术的支持，才迫使我们改变了方向。然而，难以捉摸的无线上传到 AVR 只是太接近放弃。不情愿地，我创建了另一个代码库。这一次，它是由 [Windows 通用 App](https://msdn.microsoft.com/en-us/windows/uwp/get-started/whats-a-uwp) 平台衍生而来。

几个月后，我有了一个工作版本。它能够通过蓝牙 le 上传到 ATtiny 芯片和 ATMega 芯片。

*   [米露上传者概念验证](https://www.youtube.com/watch?v=mLfFbrijakc)

[![](img/239f499deb3cc6e3dd480b343741febf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04zh7Lyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/pooh.png) 然而，当我开始尝试添加 ESP8266 支持时——好吧，事情变糟了。似乎上面列出的所有问题中，唯一解决的是增加了蓝牙 LE 支持。我的技能没有提高。

此外，还出现了另外两个问题:

1.  处理蓝牙的广告和连接。
2.  在向连接设备写入数据时，出现了一个相当严重的问题。

第一期简直是一场噩梦。我可以绕过它，但是，它非常糟糕。简而言之，在 app BluetoothLE 搜索和连接中，必须使用两个名称空间， [Windows。设备.蓝牙](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.aspx)和[窗口。Devices . Bluetooth.BluetoothAdvertisement 广告](https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.advertisement.aspx)。首先，要找到蓝牙设备，你需要植入[蓝牙引导广告观察者](https://msdn.microsoft.com/en-us/library/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.aspx)对象:

```
 // Bluetooth LE Discovery
  BluetoothLEAdvertisementWatcher bleAdvertWatcher = new BluetoothLEAdvertisementWatcher();
  public sealed partial class MainPage : Page
  {
        // Create and initialize a new watcher instance.
        bleAdvertWatcher = new BluetoothLEAdvertisementWatcher();
        bleAdvertWatcher.Received += OnAdvertisementReceived;
        bleAdvertWatcher.Stopped += OnAdvertisementWatcherStopped;
        bleAdvertWatcher.ScanningMode = BluetoothLEScanningMode.Active;
        bleAdvertWatcher.Start();
  } 
```

Enter fullscreen mode Exit fullscreen mode

我不会深究细节，但记住这个例子，这里是我如何实现 BluetoothLE 应用内扫描和连接的概述。

1.  当 OnAdvertisementReceived 触发时，您将从 EventArgs 中获取发现的设备 ID
2.  在用户发现所寻找的设备之后，用户输入将使用从 AdvertisementWatcher 中找到的 ID 来启动蓝牙设备的异步创建。
3.  这里是它变得棘手的地方:如果设备成功连接，那么就没有事件——相反，回调计时器应该启动，并有足够的时间让 BluetoothLEDevice 进行连接和枚举。
4.  当计时器回调触发时，使用新的 var device = await bluetooth ledevice。FromBluetoothAddressAsync(ID)。
5.  等待之后，services 变量应该拥有在 BluetoothLEDevice 上找到的所有服务。此时，应该枚举远程设备上的所有服务，var services = device。GattServices，包括枚举服务和特征。

API 实际期望的是用户将使用 Windows 内置的蓝牙支持连接到设备。这个 API 似乎考虑不周，很不幸。即使是苹果公司，在他们所有的“开发者指导”下，也不会在搜索和连接蓝牙设备时束缚开发者的手脚。当然，CoreBluetooth 是在 BluetoothLE 的生命周期早期开发的，所以那可能是在 API 开发人员知道不要把太多权力交给代码消费者之前？谁知道呢！但我对此事有强烈的感觉，因为我花了很长时间才弄清楚微软的意图。

就这样，我结束了 Lumi3 项目，开始着手 Lumi4。