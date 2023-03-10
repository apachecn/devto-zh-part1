# 怎么会？NET JIT a 方法？(也以“分层编译”为特色)

> 原文：<https://dev.to/mattwarren/how-does-net-jit-a-method-also-featuring-tiered-compilation-4if>

的。NET runtime (CLR)主要使用即时(JIT)编译器将你的可执行文件转换成机器码(暂时抛开[提前(AOT)场景](https://github.com/dotnet/corert/)，正如[官方微软文档所说](https://docs.microsoft.com/en-us/dotnet/standard/managed-execution-process):

> 在执行时，**实时(JIT)编译器将 MSIL 翻译成本机代码**。在编译过程中，代码必须通过一个验证过程，该过程检查 MSIL 和元数据，以确定代码是否是类型安全的。

但是这个过程实际上是如何进行的呢？

同样的文件[给了我们更多的信息](https://docs.microsoft.com/en-us/dotnet/standard/managed-execution-process):

> JIT 编译考虑到了一些代码在执行过程中可能永远不会被调用的可能性。它不是使用时间和内存将 PE 文件中的所有 MSIL 转换为本机代码，而是在执行期间根据需要转换 MSIL，并将生成的本机代码存储在内存中，以便在该进程的上下文中后续调用可以访问它。当类型被加载和初始化时，加载器**为类型中的每个方法**创建并附加一个存根。当第一次调用一个方法时，**存根将控制权传递给 JIT 编译器**，后者将该方法的 MSIL 转换成本机代码，**将存根修改为直接指向生成的本机代码**。因此，对 JIT 编译的方法的后续调用直接指向本机代码。

**真简单！！然而，如果你想知道更多，这篇文章的其余部分将详细探讨这一过程。**

此外，我们将关注一个正在成为核心 CLR 的**新特性，称为“**分层编译**”。到目前为止，这对于 CLR 来说是一个很大的变化。NET 方法只在第一次使用时被 JIT 编译过一次。分层编译正试图改变这种情况，允许方法被重新编译成更优化的版本，就像 Java Hotspot 编译器中的[一样。](http://www.oracle.com/technetwork/java/whitepaper-135217.html)**

* * *

## 工作原理

但是在我们展望未来的计划之前，**当前的 CLR 如何允许 JIT 将方法从 IL 转换为本机代码**？嗯，他们说“一张图片胜过千言万语”

#### **在方法被 JITed 之前**

[![Step 1 - Before JITing](img/589a6c392b9a7667009ef2bb67cf395e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n3WsCcyC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mattwarren.oimg/2017/12/01%2520-%2520Before%2520JITing.svg)

#### **方法被 JIT 后**

[![Step 2 - After JITing - Normal](img/3dab8abebfd22d8bb6f5a4efb4223c87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DzE9IZiS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mattwarren.oimg/2017/12/02%2520-%2520After%2520JITing%2520-%2520Normal.svg)

需要注意的主要事项是:

*   CLR 已经放入了“预编码”和“存根”来将初始方法调用转移到`PreStubWorker()`方法(最终调用 JIT)。这些是手写的汇编代码片段，仅由几条指令组成。
*   一旦该方法被 JIT 化为“本机代码”，它就创建了一个稳定的入口点。在方法的剩余生命周期中，CLR 保证这一点不会改变，因此剩余的运行时间可以依赖于它保持稳定。
*   “临时入口点”并没有消失，它仍然是可用的，因为可能有其他方法会调用它。然而，相关的“预编码修正”已被重写或“回补”，以指向新创建的“本机代码”而不是`PreStubWorker()`。
*   CLR 不改变调用被 JITted 的方法的方法中的`call`指令的地址，它只改变“预编码”中的地址。但是因为 CLR 中的所有方法调用都要经过一个预编码，所以第二次调用新的 JIT 方法时，调用将在“本机代码”处结束。

作为参考,“稳定入口点”与调用 [RuntimeMethodHandle 时返回的`IntPtr`是同一个内存位置。GetFunctionPointer()方法](https://msdn.microsoft.com/en-us/library/system.runtimemethodhandle.getfunctionpointer%28v=vs.110%29.aspx?f=255&MSPPError=-2147217396)。

如果你想亲自看看这个过程，你可以像我一样重新编译 CoreCLR 源代码并添加相关的调试信息**或者**只需使用 WinDbg 并按照这篇优秀的博客文章中的步骤[。](https://blogs.msdn.microsoft.com/abhinaba/2014/09/29/net-just-in-time-compilation-and-warming-up-your-system/)

最后，下面列出了核心 CLR 源代码的不同部分:

*   [用于“PrecodeFixupThunk”的 JIT 助手](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/inc/jithelpers.h#L295-L299)
*   [PrecodeFixupThunk (i386 组件)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/i386/asmhelpers.asm#L888-L907)
*   [ThePreStub (i386 组件)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/i386/asmhelpers.asm#L1739-L1769)
*   [预浸器(-好吧](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/prestub.cpp#L1027-L1140)
*   [MethodDesc::DoPrestub(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/prestub.cpp#L1178-L1707)
*   [MethodDesc::DoBackpatch(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/prestub.cpp#L67-L193)
*   [MethodDesc::SetStableEntryPointInterlocked(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/method.cpp#L5170-L5189)

**注意:**这篇文章并不打算研究 JIT 本身是如何工作的，如果你有兴趣的话，可以看看这个由主要开发者之一写的[精彩概述](https://github.com/CarolEidt/coreclr/blob/master/Documentation/botr/ryujit-tutorial.md#ryujit-high-level-overview)。

* * *

### JIT 与执行引擎(EE)的交互

让所有这些工作 JIT 和 EE 必须一起工作，为了了解所涉及的内容，请看一下描述决定 JIT 可以使用哪种类型的预编码的[规则的注释。所有这些信息都存储在 EE 中，因为这是唯一一个完全了解一个方法做什么的地方，所以 JIT 必须询问在哪个模式下工作。](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/inc/corinfo.h#L1426-L1514)

此外，JIT 必须询问 EE 函数入口点的地址是什么，这是通过以下方法完成的:

*   [CEE info::getFunctionEntryPoint(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/jitinterface.cpp#L8872-L8923)
    *   然后调用[method desc::TryGetMultiCallableAddrOfCode(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/method.cpp#L2218-L2324)
*   [ce insert::getfunctionfixed dentrypoint(-什么)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/jitinterface.cpp#L8925-L8955)
    *   然后调用[method desc::GetMultiCallableAddrOfCode(..)](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/method.cpp#L2187-L2209)

* * *

### 预编码和存根

有不同类型或“预编码”可用，“修复”、“远程处理”或“存根”，您可以在[method desc::GetPrecodeType()](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/method.cpp#L5747-L5773)中查看使用哪种类型的规则。此外，由于它们是如此低级的机制，它们在不同的 CPU 体系结构中以不同的方式实现，来自代码中的[注释:](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/method.hpp#L2023-L2035)

> 临时入口点有两个实现选项:
> 
> (1)紧凑入口点。它们提供了尽可能密集的入口点，但是不能被修补以指向最终代码。对 unjitted 方法的调用是通过 slot 的间接调用。
> 
> (2)预编码。预编码将被修补以最终指向最终代码，因此临时入口点可以嵌入到代码中。
> 对 unjitted 方法的调用是对直接跳转的直接调用。
> 
> 我们使用(1)表示 x86，使用(2)表示 64 位，以在每个平台上获得最佳性能。对于臂(1)使用。

在 BOTR 中还有更多关于“预编码”的信息[。](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/method-descriptor.md#precode)

最后，你不可能深入 CLR 内部而不碰到“存根”(或“蹦床”、“thunks”等)，例如它们在

*   [代表们](http://mattwarren.org/2017/01/25/How-do-.NET-delegates-work/#creation-of-the-delegate-invoke-method)
*   [虚拟方法(接口)调度](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/virtual-stub-dispatch.md#stubs)
*   [跳转存根](https://github.com/dotnet/coreclr/blob/master/Documentation/design-docs/jump-stubs.md)
*   [共享仿制药](https://github.com/dotnet/coreclr/blob/release/2.0.0/Documentation/botr/clr-abi.md#generics)
*   [Dll 导入回调](https://github.com/dotnet/coreclr/blob/release/2.0.0/src/vm/stubmgr.cpp#L1360-L1388)
*   可能还有更多我错过的！

* * *

## 分层编译

在我们进一步讨论之前，我想指出**分层编译**还在进行中。作为一个提示，要让它工作，您当前必须设置一个名为`COMPLUS_EXPERIMENTAL_TieredCompilation`的环境变量。看起来当前的工作集中在使其成为可能的基础设施上(即 CLR 变化)，那么我认为在默认启用它之前，必须进行大量的测试和性能分析。

如果你想了解这个特性的目标，以及它如何适应更广泛的“代码版本化”过程，我推荐阅读[优秀设计文档](https://github.com/dotnet/coreclr/blob/master/Documentation/design-docs/code-versioning.md)，包括[未来路线图的可能性](https://github.com/dotnet/coreclr/blob/master/Documentation/design-docs/code-versioning.md#future-roadmap-possibilities)。

为了说明到目前为止所涉及的内容，在以下方面开展了一些工作:

*   **调试器**(例如，如果分层 jitting 在附加调试器之前重新编译方法，则不会命中[断点](https://github.com/dotnet/coreclr/issues/14427)，当分层 jitting 替换代码时[源代码行断点停止工作)](https://github.com/dotnet/coreclr/issues/14423)
*   **剖析 API**——例如[分层 JIT:实现额外的剖析 API](https://github.com/dotnet/coreclr/issues/12610)
*   **诊断** -(全部通过[分层 jitting 跟踪:设计/实现适当的诊断](https://github.com/dotnet/coreclr/issues/12612)，例如[分层 Jitting:修复 IL 到 ETW 的本机映射](https://github.com/dotnet/coreclr/issues/14947))
*   解释器 - [是的，CLR 有一个内置的解释器](http://mattwarren.org/2017/03/30/The-.NET-IL-Interpreter/)
*   [其他许多地方](https://github.com/dotnet/coreclr/search?utf8=%E2%9C%93&q=tiered+compilation&type=)

如果你想跟进，你可以[看看相关的问题/策略](https://github.com/dotnet/coreclr/search?q=tiered+compilation&type=Issues&utf8=%E2%9C%93)，这里有一些主要的问题让你开始:

*   [分层编译步骤 1](https://github.com/dotnet/coreclr/pull/10478)
*   [WIP -分层 Jitting Part Deux](https://github.com/dotnet/coreclr/pull/12193)
*   Noah Falk 的所有 PRs(从事该功能的主要微软开发人员)

在[介绍分层 JIT](https://github.com/dotnet/coreclr/issues/4331) 中也有一些很好的背景信息，如果你想了解它最终将如何利用 JIT 中的变化(‘MinOpts’)，看看[低层回退](https://github.com/dotnet/coreclr/blob/master/Documentation/performance/JitOptimizerTodoAssessment.md#low-tier-back-off)和 [JIT:为层 1](https://github.com/dotnet/coreclr/pull/15046) 启用积极的内联策略。

* * *

### 历史-重组

作为一个快速的历史旁白，你以前可以让 CLR 为你重新 JIT 一个方法，但是它只能与[分析 API](https://docs.microsoft.com/en-us/dotnet/framework/unmanaged-api/profiling/profiling-overview#profiling_api)一起工作，这意味着你必须写一些 C/C++ COM 代码来实现它！此外，ReJIT 只允许在相同的级别上重新编译该方法，因此它不会产生更多优化的代码。它主要是为了帮助[监控或剖析工具](https://blogs.msdn.microsoft.com/davbr/2011/10/10/rejit-limitations-in-net-4-5/)。

* * *

## 工作原理

最后，它是如何工作的，让我们再看一些图表。首先，作为一个回顾，让我们看看一旦一个方法被 JIT 化，而**分层编译被关闭**(与上图相同)，事情会如何结束:

[![Step 2 - After JITing - Normal](img/3dab8abebfd22d8bb6f5a4efb4223c87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DzE9IZiS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mattwarren.oimg/2017/12/02%2520-%2520After%2520JITing%2520-%2520Normal.svg)

现在，作为一个比较，下面是启用了的**分层编译的相同阶段的情况:**

[![Step 3 - After JITing - Tiered Compilation](img/9b290834072a0ad7692b58bc20c4ce19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_uPjQTad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mattwarren.oimg/2017/12/03%2520-%2520After%2520JITing%2520-%2520Tiered%2520Compilation.svg)

主要区别在于，分层编译迫使方法调用通过另一个间接级别，即“pre stub”。这是为了计数方法被调用的次数，一旦达到阈值([当前为 30](https://github.com/dotnet/coreclr/blob/5d91c4d2cc8fe60bad20cdfdf2e5f239bc024061/src/vm/tieredcompilation.cpp#L84) )，将重写“pre stub”以指向“优化的本机代码”:

[![Step 4 - 04 - After Tiered Compilation Optimisation](img/b1ca264718f671f2204f40bee9bf608f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vtyjNbk2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mattwarren.oimg/2017/12/04%2520-%2520After%2520Tiered%2520Compilation%2520Optimisation.svg)

请注意，原始的“本机代码”仍然可用，因此如果需要，可以恢复更改，并且方法调用可以返回到未优化的版本。

* * *

### 使用计数器

在来自 [prestub.cpp](https://github.com/dotnet/coreclr/blob/5d91c4d2cc8fe60bad20cdfdf2e5f239bc024061/src/vm/prestub.cpp#L1702-L1715) 的评论中，我们可以看到更多关于计数器的细节:

```
 /***************************   CALL COUNTER    ***********************/
    // If we are counting calls for tiered compilation, leave the prestub
    // in place so that we can continue intercepting method invocations.
    // When the TieredCompilationManager has received enough call notifications
    // for this method only then do we back-patch it.
    BOOL fCanBackpatchPrestub = TRUE;
#ifdef FEATURE_TIERED_COMPILATION
    BOOL fEligibleForTieredCompilation = IsEligibleForTieredCompilation();
    if (fEligibleForTieredCompilation)
    {
        CallCounter * pCallCounter = GetCallCounter();
        fCanBackpatchPrestub = pCallCounter->OnMethodCalled(this);
    }
#endif 
```

本质上，“存根”回调到 [TieredCompilationManager](https://github.com/dotnet/coreclr/blob/5d91c4d2cc8fe60bad20cdfdf2e5f239bc024061/src/vm/tieredcompilation.cpp) 中，直到“分层编译”被触发，一旦发生这种情况，“存根”被“回补”以阻止它被调用。

* * *

### 为什么不是‘解读’？

如果你想知道为什么分层编译没有解释模式，你并不孤单，我问了[同样的问题](https://github.com/dotnet/coreclr/pull/10478#issuecomment-289394905)(更多信息见[。网络解释器](http://mattwarren.org/2017/03/30/The-.NET-IL-Interpreter/)

而我得到的[答案是](https://github.com/dotnet/coreclr/pull/10478#issuecomment-289412414):

> > 已经有一个可用的解释器了，还是说它不适合生产代码？
> 
> 这是一个很好的问题，但是你猜对了——解释器的状态还不足以运行生产代码。如果您想让调试和分析工具正常工作(我们确实这样做了)，还有一些重要的问题。只要有足够的时间和努力，这一切都是可以解决的，只是从这里开始并不容易。
> 
> > 非优化和优化 JITting 的开销有多大差别？
> 
> 在我的机器上，对于类似的 IL 输入大小，非优化 jitting 所用的时间大约是优化 jitting 所用时间的 65%,当然，我希望结果会因工作负载和硬件而异。检查这第一步应该更容易收集更好的测量。

但那是几个月前的事了，也许单核细胞增多症是新的。网传会变东西，[谁知道](https://twitter.com/matthewwarren/status/930397571478183937)？

* * *

### 为什么不是 LLVM？

最后，为什么他们不使用 LLVM 来编译代码，从[引入分层 JIT(注释)](https://github.com/dotnet/coreclr/issues/4331#issuecomment-313179155)

> 在 GC 和 EH 中，CLR 所需的 LLVM 支持与 Java 所需的 LLVM 支持之间存在(并且很可能仍然存在)显著的差异，并且对优化器的限制也是如此。仅举一个例子:CLRs GC 目前不能容忍指向对象末尾的托管指针。Java 通过基本/派生的成对报告机制来处理这个问题。我们要么需要将对这种成对报告的支持引入到 CLR 中，要么限制 LLVM 的优化器不创建这种类型的指针。最重要的是，LLILC jit 很慢，我们不确定它最终会产生什么样的代码质量。
> 
> 因此，弄清楚 LLILC 如何适应尚不存在的潜在多层方法似乎(现在看来仍然)为时过早。**现在
> 的想法是将分层纳入框架，并将 RyuJit 用于第二层 jit** 。随着我们了解的越来越多，我们可能会发现确实存在更高层 JIT 的空间，或者，至少，更好地理解在这些事情有意义之前我们还需要做什么。

在[介绍分层 JIT](https://github.com/dotnet/coreclr/issues/4331) 中有更多的背景信息

* * *

## 总结

我最喜欢的微软制造的副作用之一。NET 开源和开放开发是我们可以跟进的进展中的功能。能够下载最新的代码，试用它们，看看它们是如何工作的，这太棒了，为 OSS 欢呼！！