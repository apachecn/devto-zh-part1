# Genymotion vs Android 模拟器

> 原文：<https://dev.to/sumitgpt/genymotion-vs-android-emulator-2i6o>

关于选择哪个 android 模拟器或者与 Genymotion 搭配一直存在争议，我看到大多数讨论都倾向于选择 Genymotion。我收集了一些我认为最常见的用例的数据，基于这些数据，我将评估所有的 android 模拟器和 Genymotion。

**TL；DR:配置正确的话，Android 模拟器比 Genymotion 快。

使用 x86 (32 位)镜像配合 Google APIs，3GB ram，四核 CPU。**

> 唷，很高兴我们现在已经过了那个
> 
> ，让我们深潜吧

**免责声明**:我已经测试了我的用例，对我来说它看起来是一般的用例，即运行测试。
所有基准测试都是在 2015 年中期的 MacBook Pro 上完成的。

我所说的 Genymotion 指的是 **Genymotion 桌面**。他们还有其他产品，如 Genymotion on Cloud&Genymotion on Demand，这里不考虑。我并不是说 Genymotion 不够好，但与某些 Android 模拟器相比，它在运行测试时速度较慢。

关于这个主题的一点背景知识，然后我们将跳到好的方面。

我已经有了一些基准，请留下来。

很久以前，Android 模拟器是唯一的出路。但是它们用起来太慢了，原因是架构的变化。

对于运行在 x86 机器上的 ARM 仿真器，你能期待什么？每条指令都必须从 ARM 转换到 x86 架构，这使得它非常慢。

然后出现了 Android 的 x86 映像，由于摆脱了 ARM 到 x86 平台的变化，它的速度更快。

现在可以在 x86 机器上运行 x86 Android 模拟器了。

> *问题解决了！！！*
> 
> **不！**

Android 模拟器仍然比人们想要的慢。然后 Genymotion 出现了，它只是一个运行在虚拟盒子中的 Android VM。但是与运行在 qemu 上的普通旧 android 模拟器相比，它相当稳定&快。

让我们跳到今天的情况。

我在 CI 基础设施和我的机器上使用 Genymotion。当前的任务是**淘汰 CI 基础设施和机器中使用的 Genymotion** 。

> 你问为什么？
> 
> 执照要花钱。咄...

乍看之下，这似乎是一个愚蠢的举动，因为 Android 模拟器又慢又有问题，它们似乎适得其反，但当你深入到这种情况的本质时，你会发现 Android 模拟器实际上更优越。

我的用例是对它们进行集成测试(主要是 espresso)，测试次数刚好超过 1100 次。Genymotion 运行所有测试大约需要 23 分钟。

基因运动面临的其他几个问题？

*   有限的命令行工具( [GMTool](https://docs.genymotion.com/Content/04_Tools/GMTool/GMTool.htm) )。
*   由于内存问题，他们需要定期重启。这是一项手动任务，想象一下在有许多机器的 CI 基础架构上完成这项任务。

**进入安卓模拟器**

当你第一次尝试设置其中一个时，它会给你很多选择，让你感觉就像在地铁餐厅一样。

最大的问题是 x86 还是 x86_64 以及有没有 Google APIs。

我对这些组合做了一些研究和基准测试，这就是我们得出的结果。

一连串急速的击鼓...

> 竞争的赢家是 **x86 搭配 Google API**
> 
> 但是如何呢？为什么？

好吧，我会告诉你他们每个人的问题。

与 x86 相比，x86_64 较慢

> 取决于你要价多少。
> 
> **28.2%** 多！！！

使用 Google APIs 的模拟器更稳定，没有它们的话会崩溃。

这让我们得出结论，最好的是带 Google API 的 **x86。**

在我们让获胜的模拟器与 Genymotion 竞争之前。没有什么更重要的细节了。

*   我使用过谷歌 API 的 Nexus 5 系统映像。
*   我注意到给模拟器更少的内存导致了很多 Google API 崩溃。因此，我已经解决了 3GB 的内存模拟器。
*   仿真器有一个四核。
*   HAXM 安装在主机上。

**少数基准时间**

[![Genymotion and Android Emulator Espresso Benchmark](img/7d4c94a7def1f157a739cd0d918b9400.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0edaQ_jQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.plightofbyte.com/asseimg/genymotion-vs-android-emulator/EspressoBenchmark.png)

[![Linpack](img/a5bf58eb571448750339702c3d29c461.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IdGVDfgZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.plightofbyte.com/asseimg/genymotion-vs-android-emulator/Linpack.png)

[![Geekbench 4](img/044b02885a55c0c08d834508af661fb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MHN89yk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.plightofbyte.com/asseimg/genymotion-vs-android-emulator/Geekbench4.png)

从基准测试中，你可以看到 Android emulator 在 Geekbench4 中击败了 Genymotion expect，对我来说，这更像是一个虚拟盒子击败了 qemu。

> 向模拟器之王致敬

我们现在有了更快的测试执行时间，更好的命令行工具。还有最新的安卓模拟器，事情有了新的进展。更快的启动时间等等。

谷歌一直在努力工作

> 让 Android 模拟器再次变得伟大

如果你已经有一段时间没有使用 android 模拟器了。我建议你重游，省点钱。

我尝试过的另一个解决方案是在 AWS 上运行 Android-x86 映像。

我能够在 vSphere ESXi 虚拟机管理程序上运行它，但无法在 AWS 或任何其他云平台上运行。如果有人知道关于它的任何事情，请在下面评论。

PS: [VMWare 现在在 AWS 上可用](https://aws.amazon.com/vmware/)， [Android-x86](http://www.android-x86.org) 在 AWS 上或许终究是可能的。

如何看待 Genymotion 桌面和 Android 模拟器？下面评论。

喜欢这篇文章就分享一下。