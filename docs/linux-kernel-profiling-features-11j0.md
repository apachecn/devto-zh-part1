# Linux 内核分析特性

> 原文：<https://dev.to/dzeban/linux-kernel-profiling-features-11j0>

## [T1】简介](#intro)

有时，当您面临真正严重的性能问题时，仅仅分析您的应用程序是不够的。正如我们在使用 [gprof、gcov](https://dev.to/blog/gprof-gcov/) 和 [Valgrind](https://dev.to/blog/valgrind/) 分析我们的应用程序时所看到的，问题出在我们的应用程序下面的某个地方——某些东西在长时间的 I/O 等待周期中阻碍了`pread`。

如何跟踪系统调用乍一看并不清楚——有各种内核分析器，它们都以自己的方式工作，需要独特的配置、方法、分析等等。是的，真的很难搞清楚。作为由大型社区开发的最大的开源项目，Linux 吸收了几种不同的、有时是相互冲突的剖析工具。从某种意义上说，情况变得更糟了——当一些概要文件趋向于合并( *ftrace* 和 *perf* )时，其他工具出现了——最后一个例子是 *ktap* 。

为了理解这个集市，让我们从底层开始——内核拥有什么使它能够剖析它？基本上，只有 3 个内核工具支持概要分析:

*   内核跟踪点
*   内核探测器
*   性能事件

这些特性让我们能够访问内核内部。通过使用它们，我们可以测量内核功能的执行，跟踪对设备的访问，分析 CPU 状态等等。

这些特性很难直接使用，只能从内核访问。好吧，如果你真的想，你可以写你自己的 Linux 内核模块，将利用这些设施为你的自定义使用，但这几乎是毫无意义的。这就是为什么人们创造了一些非常好的通用分析器:

*   ftrace
*   性能
*   SystemTap
*   ktap

所有这些都是基于那个特性的，稍后会更彻底地讨论，但是现在让我们回顾一下特性本身。

## 内核痕迹点

内核跟踪点是一个通过静态检测 <sup id="fnref:kernel-markers">[1](#fn:kernel-markers)</sup> 来跟踪内核函数的框架。

跟踪点是代码中可以绑定回调的地方。*跟踪点*可以禁用(无回调)和启用(有回调)。虽然它仍然是轻量级的，但可能会有几次回调——当回调被禁用时，它实际上看起来像`if (unlikely(tracepoint.enabled))`。

*Tracepoint* 输出写入环形缓冲区，在`/sys/kernel/debug/tracing/trace`通过 *debugfs* 导出。在`/sys/kernel/debug/tracing/events`还有整个可追踪事件树，它导出控制文件来启用/禁用特定事件。

尽管名为*,跟踪点*是基于事件分析的基础，因为除了跟踪之外，你还可以在回调中做任何事情，例如标记时间戳和测量资源使用。Linux 内核(从 2.6.28 开始)已经在许多地方使用了跟踪点。比如 [`__do_kmalloc`](http://lxr.free-electrons.com/source/mm/slab.c?v=3.12#L3714) :

```
/**
 * __do_kmalloc - allocate memory
 * @size: how many bytes of memory are required.
 * @flags: the type of memory to allocate (see kmalloc).
 * @caller: function caller for debug tracking of the caller
 */
static __always_inline void *__do_kmalloc(size_t size, gfp_t flags,
                                          unsigned long caller)
{
        struct kmem_cache *cachep;
        void *ret;

        /* If you want to save a few bytes .text space: replace
         * __ with kmem_.
         * Then kmalloc uses the uninlined functions instead of the inline
         * functions.
         */
        cachep = kmalloc_slab(size, flags);
        if (unlikely(ZERO_OR_NULL_PTR(cachep)))
                return cachep;
        ret = slab_alloc(cachep, flags, caller);

        trace_kmalloc(caller, ret,
                      size, cachep->size, flags);

        return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

`trace_kmalloc`是*的痕迹点*。在内核的其他关键部分还有很多其他的，比如调度程序、块 I/O、网络甚至中断处理程序。大多数分析器都使用它们，因为它们的开销很小，由事件触发，并且使您不必修改内核。

好了，现在你可能急于将它插入到所有的内核模块中，并对它进行剖析，但是要小心。如果你想添加*跟踪点*，你必须有很大的耐心和技巧，因为编写自己的跟踪点是非常丑陋和笨拙的。你可以在 [*样本/痕迹 _ 事件/*](http://lxr.free-electrons.com/source/samples/trace_events/?v=3.13) 看到例子。在引擎盖下*痕迹点*是一个 C 宏黑魔法，只有勇敢无畏的人才能理解。

即使你做了所有疯狂的宏声明和结构定义，如果你有了`CONFIG_MODULE_SIG=y`而没有给模块签名，它也可能根本不起作用。这看起来有点奇怪的配置，但实际上，它是所有主流发行版的默认配置，包括 Fedora 和 Ubuntu。也就是说，经过 9 圈地狱，你将一无所有。

所以，只要记住:

> 只使用内核中现有的跟踪点，不要创建自己的。

现在我要解释为什么会这样。因此，如果你厌倦了跟踪点，就跳过阅读 T2 探测器。

好了，所以前段时间在准备内核 3.1 <sup id="fnref:commit-taints">[2](#fn:commit-taints)</sup> 的时候增加了这个代码:

```
static int tracepoint_module_coming(struct module *mod)
{
          struct tp_module *tp_mod, *iter;
          int ret = 0;

          /*
           * We skip modules that tain the kernel, especially those with different
           * module header (for forced load), to make sure we don't cause a crash.
           */
          if (mod->taints)
                  return 0; 
```

Enter fullscreen mode Exit fullscreen mode

如果模块被污染，我们不会写任何跟踪点。后来越来越合适了

```
/*
 * We skip modules that taint the kernel, especially those with different
 * module headers (for forced load), to make sure we don't cause a crash.
 * Staging and out-of-tree GPL modules are fine.
 */
if (mod->taints & ~((1 << TAINT_OOT_MODULE) | (1 << TAINT_CRAP)))
        return 0; 
```

Enter fullscreen mode Exit fullscreen mode

比如，好吧，它可能是树外(`TAINT_OOT_MODULE`)或暂存(`TAINT_CRAP`)，但任何其他的都是不允许的。

似乎是合法的，对吧？现在，如果你的内核编译时启用了`CONFIG_MODULE_SIG`，而你漂亮的模块没有签名，你会怎么想？嗯，模块加载器会为它设置`TAINT_FORCES_MODULE`标志。现在你漂亮的模块将永远不会通过`tracepoint_module_coming`中的条件，也不会向你显示任何跟踪点输出。正如我前面说过的，这个愚蠢的选项是为所有主要发行版设置的，包括从内核版本 3.1 开始的 Fedora 和 Ubuntu。

如果你认为——“好吧，让我们签署该死的模块！”你错了。模块必须用内核私有密钥签名，这个密钥由你的 Linux 发行版供应商持有，当然，对你来说是不可用的。

整个恐怖的故事在 lkml [1](https://lkml.org/lkml/2014/2/13/488) 、 [2](https://lkml.org/lkml/2014/3/4/925) 都有。

对我来说，我只是引用了 Steven Rostedt (ftrace 维护者和 tracepoints 开发者之一)最喜欢的东西

```
> OK, this IS a major bug and needs to be fixed. This explains a couple
> of reports I received about tracepoints not working, and I never
> figured out why. Basically, they even did this:
> 
> 
> trace_printk("before tracepoint\n");
> trace_some_trace_point();
> trace_printk("after tracepoint\n");
>
> Enabled the tracepoint (it shows up as enabled and working in the
> tools, but not the trace), but in the trace they would get:
>
> before tracepoint
> after tracepoint
>
> and never get the actual tracepoint. But as they were debugging
> something else, it was just thought that this was their bug. But it
> baffled me to why that tracepoint wasn't working even though nothing in
> the dmesg had any errors about tracepoints.
> 
> Well, this now explains it. If you compile a kernel with the following
> options:
> 
> CONFIG_MODULE_SIG=y
> # CONFIG_MODULE_SIG_FORCE is not set
> # CONFIG_MODULE_SIG_ALL is not set
> 
> You now just disabled (silently) all tracepoints in modules. WITH NO
> FREAKING ERROR MESSAGE!!!
> 
> The tracepoints will show up in /sys/kernel/debug/tracing/events, they
> will show up in perf list, you can enable them in either perf or the
> debugfs, but they will never actually be executed. You will just get
> silence even though everything appeared to be working just fine. 
```

Enter fullscreen mode Exit fullscreen mode

概述:

*   内核跟踪点是一个轻量级的跟踪和分析工具。
*   Linux 内核配备了大量的*跟踪点*，这些跟踪点被大多数评测器使用，尤其是被 *perf* 和 *ftrace* 使用。
*   跟踪点是非常神奇的，几乎不可能在内核模块中使用。
*   在您的 LKM，如果出现以下情况，它将不起作用:
    *   内核版本> =3.1(可能在 3.15 中得到修复)
    *   `CONFIG_MODULE_SIG=y`
    *   您的模块没有用内核私钥签名。

## 内核探针

Kernel probes 是一个动态调试和分析机制，它允许你进入内核代码，调用你的自定义函数**probes**并返回所有内容。

基本上，这是通过编写内核模块来完成的，在内核模块中，您为内核代码中的一些地址或符号注册一个处理程序。同样根据`struct kprobe` 的[定义，你可以从地址传递偏移量，但是我不确定。在你注册的处理程序中，你可以做任何事情——写日志，通过 sysfs 导出缓冲区，测量时间和无限的可能性。这与*跟踪点*形成鲜明对比，在那里你只能从调试器中读取日志。](http://lxr.free-electrons.com/source/include/linux/kprobes.h?v=3.13#L73)

有 3 种类型的探头:

*   *k probes*–允许您闯入任何内核地址的基本探测器。
*   j probes–跳转探针，插入到函数的开头，让你方便地访问函数参数；这有点像代理功能。
*   *kret probes*–返回插入函数返回点的探针。

后两种类型基于基本的*k 探测器*。

所有这些通常是这样工作的:

*   我们在某个地址 a 上注册探针。
*   *kprobe* 子系统发现一个。
*   kprobe 在地址 a 复制指令。
*   *kprobe* 替换断点 A 处的指令(x86 的情况下为`int 3`)。
*   现在，当执行命中被探测的地址 A 时，就会出现 CPU 陷阱。
*   寄存器被保存。
*   CPU 通过`notifier_call_chain`机制将控制权转移给 *kprobes* 。
*   最后， *kprobes* 调用我们的处理程序。
*   毕竟，我们恢复寄存器，在 A 处复制回指令并继续执行。

我们的处理程序通常获取断点发生的地址作为参数，并在`pt_args`结构中注册值。 *kprobes* 处理程序原型:

```
 typedef int (*kprobe_break_handler_t) (struct kprobe *, struct pt_regs *); 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，除了调试之外，这些信息是没有用的，因为我们有 *jprobes* 。 *jprobes* handler 的原型和拦截功能完全一样。例如，这是`do_fork` :
的处理程序

```
 /* Proxy routine having the same arguments as actual do_fork() routine */
    static long jdo_fork(unsigned long clone_flags, unsigned long stack_start,
              struct pt_regs *regs, unsigned long stack_size,
              int __user *parent_tidptr, int __user *child_tidptr) 
```

Enter fullscreen mode Exit fullscreen mode

另外， *jprobes* 不会导致中断，因为它在轻量级的`setjmp/longjmp`的帮助下工作。

最后，最方便的分析工具是 *kretprobes* 。它允许您注册两个处理程序——一个在函数开始时调用，另一个在函数结束时调用。但是真正酷的特性是它允许你保存两次调用之间的状态，比如时间戳或者计数器。

与其千言万语——不如看看 [*样本/kprobes*](http://lxr.free-electrons.com/source/samples/kprobes/?v=3.13) 绝对惊人的样本。

概述:

*   kprobes 是一个漂亮的动态调试、跟踪和分析工具。
*   这是非侵入性分析的一个基本内核特性。

## 性能事件

*perf_events* 是在 PMU(性能监控单元)中实现的硬件指标的接口，PMU 是 CPU 的一部分。

得益于 *perf_events* ，无论您使用的是 x86 还是 ARM 架构，您都可以轻松请求内核显示 L1 缓存未命中计数。这里列出了 perf 支持哪些 CPUs】。

除此之外，perf 还包括各种内核指标，如软件上下文切换计数(`PERF_COUNT_SW_CONTEXT_SWITCHES`)。

除此之外，perf 还包括通过`ftrace`支持的*跟踪点*。

为了访问*性能事件*，有一个特殊的系统调用 [`perf_event_open`](http://web.eece.maine.edu/~vweaver/projects/perf_events/perf_event_open.html) 。您正在传递事件的类型(硬件、内核、跟踪点)和所谓的配置，其中您根据类型指定您确切想要的内容。在跟踪点的情况下，它是一个函数名，在硬件的情况下，它是一个 CPU 指标，等等。

除此之外，还有一大堆东西，比如事件组、过滤器、采样、各种输出格式等等。所有这些都是[不断地破坏](http://web.eece.maine.edu/~vweaver/projects/perf_events/abi_breakage.html) <sup id="fnref:abi-breakage">[3](#fn:abi-breakage)</sup> ，这就是为什么你唯一能要求 perf_events 的是特殊的`perf`实用程序——唯一的用户空间实用程序，它是内核树的一部分。

*perf_events* 以及与之相关的所有事情就像瘟疫一样在内核中蔓延，现在`ftrace`即将成为`perf`的一部分( [1](http://thread.gmane.org/gmane.linux.kernel/1136520) ， [2](https://lkml.org/lkml/2013/10/16/15) )。有些人对与 *perf* 相关的事情反应过度，尽管这没有用，因为 *perf* 是由内核大鱼——Ingo Molnar<sup id="fnref:molnar">T13】4T15】和 Peter Zijlstra 开发的。</sup>

脱离`perf`我真的说不出更多关于 *perf_events* 的事情了，就到此为止。

## 总结

有几个 Linux 内核特性支持概要分析:

1.  *追踪点*
2.  *kprobes*
3.  *性能事件*

所有的 Linux 内核分析器都使用这些特性的一些组合，请阅读特定分析器的文章中的详细信息。

## 阅读

*   [https://events . Linux foundation . org/sites/events/files/slides/kernel _ profiling _ debugging _ tools _ 0 . pdf](https://events.linuxfoundation.org/sites/events/files/slides/kernel_profiling_debugging_tools_0.pdf)
*   [http://events . Linux foundation . org/sites/events/files/lcjp 13 _ zanoni . pdf](http://events.linuxfoundation.org/sites/events/files/lcjp13_zannoni.pdf)
*   *追踪点*:
    *   [Documentation/trace/trace points . txt](http://lxr.free-electrons.com/source/Documentation/trace/tracepoints.txt?v=3.13)
    *   [http://lt TNG . org/files/thesis/desnoyers-disserious-2009-12-v 27 . pdf](http://lttng.org/files/thesis/desnoyers-dissertation-2009-12-v27.pdf)
    *   [http://lwn.net/Articles/379903/](http://lwn.net/Articles/379903/)
    *   [http://lwn.net/Articles/381064/](http://lwn.net/Articles/381064/)
    *   [http://lwn.net/Articles/383362/](http://lwn.net/Articles/383362/)
*   *k 探测器*:
    *   [Documentation/k probes . txt](http://lxr.free-electrons.com/source/Documentation/kprobes.txt?v=3.13)
    *   [https://lwn.net/Articles/132196/](https://lwn.net/Articles/132196/)
*   *性能事件*:
    *   [http://web.eece.maine.edu/~vweaver/projects/perf_events/](http://web.eece.maine.edu/~vweaver/projects/perf_events/)
    *   [https://lwn.net/Articles/441209/](https://lwn.net/Articles/441209/)

* * *

1.  跟踪点是对称为内核标记早期特性的改进。<sup>【返回】</sup>
2.  即在提交时[b 75 ef 8 b 44 B1 CB 95 F5 a 26484 b 0 e 2 Fe 37 a 63 b 12 b 44](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b75ef8b44b1cb95f5a26484b0e2fe37a63b12b44)<sup>【返回】</sup>
3.  这是独立的行为。内核 **ABI** 一点也不稳定，API 才是。<sup>【归来】</sup>
4.  当前默认 O(1)进程调度程序 CFS 的作者-完全公平调度程序。<sup>【返回】</sup>