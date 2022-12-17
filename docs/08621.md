# Lumi4 -单元和集成测试

> 原文：<https://dev.to/ladvien/lumi4-unit-and-integration-tests-300l>

## 单元和集成测试

我在之前的一篇文章中提到，我很难区分单元测试和集成测试。但是这种区别对于实现测试来说是至关重要的，这些测试可能会覆盖设计用于与嵌入式系统交互的框架。至少，在我看来。下面是我如何为 Lumi4 代码库构建测试的概要。

## [Lumi4。测试](https://github.com/Ladvien/Lumi4/tree/master/Lumi4.Tests)

单元测试命名空间将包含所有测试，这些测试涵盖了可以独立操作的方法，无需与程序本身之外的任何其他系统进行通信。

例如，

```
 [TestClass]
    public class Constructor
    {
        [TestMethod]
        public void WifiCentralManagerConstructor_Null_Exception()
        {
            bool ThrewNull = false;
            try
            {

                WifiCentralManager wifiCentralManager = new WifiCentralManager(null);
            }
            catch (Exception ex)
            {
                ThrewNull = true;
            }
            Assert.IsTrue(ThrewNull);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的测试覆盖了一个构造函数方法，它应该总是能够有效地执行，而不需要与

## [Lumi4。集成测试](https://github.com/Ladvien/Lumi4/tree/master/Lumi4.IntegrationTests)

在之前的一篇文章中，我回顾了集成测试和单元测试之间显而易见的区别。集成测试实际上是针对依赖外部系统的代码的；数据库查询结果、文件流中的字符或 UART 设备中的字符。对于 Lumi4，程序依赖于三个系统。

1.  远程蓝牙设备
2.  远程 Wifi 设备
3.  英特尔十六进制文件流

对于前两个，我决定将重点放在集成测试上，而不是模拟和存根。我的理由是双重的，我很可能会调整远程设备的固件。其次，我还不懂 mocks 和 stubs。努力专注于 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) 。

当然，当我最终制定出一个行动计划时，一场新的斗争开始了。我的许多蓝牙和 Wifi 处理依赖于异步回调。这在 MSTesting(我在这次迭代中使用的测试包)中并不是最容易解决的事情。不过，最终我还是拼凑出了下面的逻辑

```
 [TestMethod]
    public async Task Search_FindsWebServer_ValidIp()
    {
        var localNetwork = Lumi4IntegrationTestSettings.LocalIP;
        WifiCentralManager wifiCentralManager = new WifiCentralManager(localNetwork);
        bool foundDevice = false;
        wifiCentralManager.DiscoveredDevice += delegate (object obj, DiscoveredDeviceEventArgs args)
        {
            if (args.DiscoveredPeripheral != null) { foundDevice = true; }
        };
        wifiCentralManager.Search(90, 120);
        await Task.Delay(Lumi4IntegrationTestSettings.SearchWifiCallbackDelay);
        Assert.IsTrue(foundDevice);
    } 
```

Enter fullscreen mode Exit fullscreen mode

有一些输入必须手动提供给测试，例如，本地 IP 和目标设备的 IP。该方法顶部的作用域是一个标志，用于标识是否找到了该设备。然后，它获取这些信息，设置一个 in 方法委托(回调)，并尝试联系设备。最后，还有一个异步延迟，其目的是让搜索有足够的时间正确执行。如果测试在给定的时间内找到了设备，就触发回调，并将标志设置为真。否则，它返回失败。

不确定有效性，但这是我得到的(到目前为止)。