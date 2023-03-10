# 当 QueryPerformanceCounter 调用花费很长时间时

> 原文：<https://dev.to/reg__/when-queryperformancecounter-call-takes-long-time-24a1>

[**QueryPerformanceCounter**](https://msdn.microsoft.com/en-us/library/windows/desktop/ms644904(v=vs.85).aspx)函数都是关于测量时间和分析性能的，所以我无法制定正确的 Google query 来找到问题的解决方案——调用`QueryPerformanceCounter`函数本身花费了太多时间。下面我描述了我最终发现的东西。

一切都是从硬件故障开始的。我的主板停止工作，所以我需要买一个新的(华硕 X370 黑仔 SLI)。我知道正常换主板需要重装 Windows，但我尽量不这么做。系统不想启动，我用 pendrive 配合 Windows installer 启动了 PC，启动了修复功能。这很有帮助——从那以后，Windows 可以启动了，一切似乎都正常...直到我启动了我在那台机器上开发的程序。它跑得非常慢。

我尝试了不同的方法来了解正在发生的事情。硬盘或网络的输入/输出不是问题。GPU 性能也还可以。这款应用程序似乎只是在缓慢地进行计算，就像 CPU 非常慢一样。我仔细检查了实际的 CPU 和 RAM 频率，但它是正常的。最后，我启动了采样分析器(Visual Studio 中嵌入的那个——命令:分析>性能分析器)。这样我发现大部分时间都花在函数`QueryPerformanceCounter`上。

这个 WinAPI 函数是在 Windows 中获取时间戳的推荐方法。它非常精确，单调，在多个内核和线程上使用安全，它具有独立于 CPU 电源管理或睿频加速的稳定频率...这很棒，但为了满足所有这些要求，Windows 可能会使用不同的方法来实现它，如文章[获取高分辨率时间戳](https://msdn.microsoft.com/en-us/library/windows/desktop/dn553408(v=vs.85).aspx)中所述。有些很快(只读取 TSC 寄存器)，有些很慢(需要系统调用-转换到内核模式)。

我写了一个简单的 C++程序，测试执行`QueryPerformanceCounter`函数需要多长时间。可以在这里看到代码:[**queryperformancecountertest . CPP**](https://github.com/sawickiap/MISC/blob/master/QueryPerformanceCounterTest.cpp)在这里下载 64 位二进制:[**queryperformancecountertest . zip**](http://asawicki.info/Download/Code/QueryPerformanceCounterTest.zip)。在两台不同的机器上运行该测试得到了以下结果:

CPU:英特尔酷睿 i7-6700K，主板:千兆 Z170-HD3-CF:

> > QueryPerformanceCounterTest.exe 1000000000
> 
> 执行 QueryPerformanceCounter x 100000000...
> 
> 根据 GetTickCount64 用时 0:00:11.312(每次调用 11.312 ns)
> T5】根据 QueryPerformanceCounter 用时 0:00:11.314(每次调用 11.314 ns)

CPU: AMD 锐龙 7 1700X，主板:华硕 X370 黑仔 SLI(不重装系统由不同型号更改):

> > QueryPerformanceCounterTest.exe 10000000
> 
> 执行 QueryPerformanceCounter x 10000000...
> 
> 根据 GetTickCount64 用时 0:00:24.906(每次调用 2490.6 ns)
> T5】根据 QueryPerformanceCounter 用时 0:00:24.911(每次调用 2491.1 ns)

如您所见，该函数在第一平台上花费了 11 纳秒，在第二平台上花费了 2.49 微秒(220 倍以上！)第二个上。这就是我的计划进展缓慢的原因。程序多次调用这个函数。

我试图修复它，并设法说服 Windows 使用快速实现。我卸载并重新安装了主板驱动程序-从制造商网站下载的最新驱动程序。我升级和降级 BIOS 到不同的版本。我从 Windows 安装介质启动系统，并再次“修复”它。我恢复了 UEFI/BIOS 中的默认设置，并试图将“ACPI HPET 表”选项改为禁用/启用/自动。什么都没用。最后，我将 Windows 恢复到出厂设置(设置>更新和安全>恢复>重置这台电脑)。这解决了我的问题，但不幸的是这就像从零开始重新安装 Windows 现在我需要再次安装和配置所有的应用程序。之后，该函数在这台机器上需要 22 ns。

我从这次“冒险”中得出两个结论:

1.  函数`QueryPerformanceCounter`在某些平台上缓慢执行是有效的，比如 2.5 微秒。如果你在每个渲染帧只调用它一次，那没关系，但是你不应该用它来分析你代码的每一小部分，调用它几百万次。
2.  Windows 10 换主板还是需要重装。否则，即使它看起来工作正常，您也可能会遇到类似这样的奇怪问题。

更新 2017-12-11:一位同事告诉我，使用“bcdedit”系统命令启用/禁用 HPET 可能有助于解决这个问题。