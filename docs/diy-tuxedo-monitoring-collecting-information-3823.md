# DIY Tuxedo 监控-收集信息

> 原文：<https://dev.to/aivarsk/diy-tuxedo-monitoring-collecting-information-3823>

有一次，我开始为 Oracle Tuxedo 构建一个监控解决方案，所以这些是我一路上的笔记。最好的开始方式是阅读[监控你的 Tuxedo 应用](https://docs.oracle.com/cd/E13161_01/tuxedo/docs10gr3/ada/admon.html)。

我希望从监控工具中找到耗时最长或调用过于频繁的瓶颈服务。甚至可能是 Tuxedo 服务的类似 profiler 的输出。获取 Tuxedo 系统和应用程序的信息有三种主要方式。

## MIB

MIB 是一块共享内存，包含关于系统的静态(UBBCONFIG)和动态信息。通过 tmadmin 命令使用它的一种常见方法是

*   *bbs* 显示服务器、服务和队列的数量
*   *pt* 显示未完成交易的信息
*   *psc* 给出服务执行的数量
*   *psr* 给出完成的请求数和完成的加载数

这就是 [AppDynamics Tuxedo 监控扩展](https://github.com/Appdynamics/tuxedo-monitoring-extension)用来收集信息的内容。

一种不太常见的方法是编写一个调用。TMIB 服务或使用 tmadmcall()函数来检索信息。这种方法有很多好处:您不必解析 tmadmin 的输出，而是使用 FML32 APIs。它还返回比 tmadmin 更多的信息，包括 tpenqueue()、tpcall()、tpcommit()和其他 ATMI 调用的数量。我遇到的一个问题是使用 tmadmcall()而不是调用。TMIB 服务的缺点是它返回有限数量的条目，并且不支持游标来检索其余的条目。但是 tmadmcall()很棒，因为它不进行服务调用。

您可以在 Tuxedo 参考手册 TM_MIB(5)和 tmadmcall(3c)中找到有关这方面的更多信息。

## tmtrace

tmtrace 允许您通过将有关 ATMI 调用的记录写入用户日志来跟踪 Tuxedo 应用程序的执行。你可以写一个程序来解析用户日志并提取有用的信息。与 MIB 提供的信息相比，您可以发现每个服务执行花费了多少时间，它调用了哪些服务，它等待其他服务返回花费了多少时间，等等。

```
 111401.mordor!IN.6769.3053347840.0: TRACE:at: { tpservice({"IN", 0x0, 0x0x1bca548, 4096, 0, 0, {1453367641, 0, 5}})
111401.mordor!IN.6769.3053347840.0: TRACE:at: { tpforward("HUB", 0x0x1bca548, 0, 0x0)
111401.mordor!IN.6769.3053347840.0: TRACE:ia: { tpacall("HUB", 0x0x1bca548, 0, 0x1000002)
111401.mordor!IN.6769.3053347840.0: TRACE:ia: } tpacall = 0 [CLIENTID {1453367641, 0, 5}]
111401.mordor!IN.6769.3053347840.0: TRACE:at: } tpforward [long jump]
111401.mordor!IN.6769.3053347840.0: TRACE:at: } tpservice
111401.mordor!HUB.6768.1951171584.0: TRACE:tr: trace("*:ulog:dye")
111401.mordor!HUB.6768.1951171584.0: TRACE:tr: dye
111401.mordor!HUB.6768.1951171584.0: TRACE:at: { tpservice({"HUB", 0x0, 0x0x19d3548, 4096, 0, 0, {1453367641, 0, 5}})
111401.mordor!HUB.6768.1951171584.0: TRACE:at: { tpacall("SVCA", 0x0x19d3548, 0, 0xc)
111401.mordor!HUB.6768.1951171584.0: TRACE:at: } tpacall = 0 [CLIENTID {1453367641, 0, 5}] 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，从 Tuxedo 9 开始，您可以使用 tputrace(3c)来实现在每个跟踪点上调用的自己的函数。因此，不需要解析用户日志，您可以通过决定写什么来减少性能损失，并跳过跟踪点，如 tpalloc()/tpfree()，何时写(聚合)和在哪里写(共享内存、网络等)。).您还可以使用 tputrace()将某些服务的请求或响应存储到日志文件或数据库中。

## 【TSAM+

TSAM+是监控 Tuxedo 系统和应用程序的官方解决方案。它收集所有的数据并在 GUI 中显示出来。一个有趣的特性是它收集完整的调用链，您可以跟踪从 WebLogic 到 Tuxedo 再到 Oracle 数据库的每个调用。

TSAM+使用另一种方法收集信息:事实证明，Tuxedo 可以加载插件，并有一个注册表来管理和配置它们。它还允许你编写实现“引擎/性能/监控”接口的自定义插件，并为你自己的需求和工具存储一些信息，尽管我认为这没有多大意义，因为你已经拥有了 TSAM+提供的一切。

但是强大的力量伴随着巨大的代价。

那么，如果你想要强大的力量，但没有价格，并建立自己的一切呢？虽然没有记录，但是可以为另一个“引擎/tsam/代理”接口实现一个插件。你必须自己找出 API，如果你有一些调试技巧和 ATMI 调用的经验，这应该不成问题。我知道这是可行的，但我不是律师，所以在走这条路之前要三思。