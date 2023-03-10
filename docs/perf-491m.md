# 性能

> 原文：<https://dev.to/dzeban/perf-491m>

## 性能概述

Perf 是一个由内核基础设施和用户空间工具组成的工具，内核基础设施用于收集各种事件，用户空间工具用于从内核获取收集的数据并对其进行分析。它就像一个 gprof，但是它是非侵入性的、低开销的，并且可以分析整个堆栈，包括你的应用程序、库、系统调用和带 CPU 的内核！

`perf`工具支持一系列可测量的事件，您可以使用`perf list`命令查看这些事件。该工具和底层内核接口可以测量来自不同来源的事件。例如，一些事件是纯内核计数器，在这种情况下，它们被称为软件事件。示例包括上下文切换、小错误、页面错误等。

事件的另一个来源是处理器本身及其性能监控单元(PMU)。它提供了一个事件列表来衡量微架构事件，如周期数、失效指令、L1 缓存未命中等。这些事件简称为“PMU 硬件事件”或“硬件事件”。它们因每种处理器类型和型号而异——查看【Vince Weaver 的性能页面了解详细信息

“perf_events”接口还提供了一小组常见的硬件事件名字对象。在每个处理器上，这些事件被映射到 CPU 提供的实际事件上，如果它们存在的话，否则，这些事件就不能被使用。有些令人困惑的是，这些事件也被称为硬件事件和硬件缓存事件。

最后，还有由内核基础设施实现的跟踪点事件。这些只有 2.6.3x 和更新的内核才有。

得益于用户空间工具(见下文)如此多样的事件和分析能力，`perf`是 Linux 系统跟踪和分析领域的一条大鱼。这是一个真正的多功能工具，我知道它有几种用途:

1.  记录一个配置文件，稍后进行分析:`perf record` + `perf report`
2.  收集应用程序或系统的统计数据:`perf stat`
3.  实时功能分析:`perf top`
4.  跟踪应用程序或系统:`perf trace`

这些方法中的每一种都包含了大量的排序、过滤、分组等等的可能性。

但是正如有人所说，`perf`是一个强大的工具，只需要一点文档。所以在这篇文章里，我会试着分享一些关于它的知识。

## 基本绩效工作流程

### 飞行前检查

当您开始使用 Perf 时，要做的第一件事是启动`perf test`。这将检查您的系统和内核特性，并报告是否有不可用的东西。通常，你需要尽可能多的“OK”。但是要注意，当在“root”和普通用户下启动时,`perf`会有不同的行为。让你做一些没有 root 权限的事情，已经够聪明了。在“/proc/sys/kernel/perf _ event _ paranoid”中有一个控制文件，您可以调整该文件以更改对性能事件的访问:

```
$ perf stat -a
Error:
You may not have permission to collect system-wide stats.
Consider tweaking /proc/sys/kernel/perf_event_paranoid:
 -1 - Not paranoid at all
  0 - Disallow raw tracepoint access for unpriv
  1 - Disallow cpu events for unpriv
  2 - Disallow kernel profiling for unpriv 
```

Enter fullscreen mode Exit fullscreen mode

你玩了`perf test`之后，就可以看到有哪些硬件事件是你用`perf list`可以获得的。同样，根据当前用户 id 的不同，列表也会有所不同。此外，许多事件将取决于您的硬件:x86 _ 64 CPUs 比一些低端 ARM 处理器有更多的硬件事件。

### 系统统计

现在是真正的侧写。要检查系统的总体健康状况，您可以使用`perf stat`收集统计数据。

```
# perf stat -a sleep 5

 Performance counter stats for 'system wide':

      20005.830934 task-clock (msec) # 3.999 CPUs utilized (100.00%)
             4,236 context-switches # 0.212 K/sec (100.00%)
               160 cpu-migrations # 0.008 K/sec (100.00%)
             2,193 page-faults # 0.110 K/sec                  
     2,414,170,118 cycles # 0.121 GHz (83.35%)
     4,196,068,507 stalled-cycles-frontend # 173.81% frontend cycles idle (83.34%)
     3,735,211,886 stalled-cycles-backend # 154.72% backend cycles idle (66.68%)
     2,109,428,612 instructions # 0.87 insns per cycle        
                                                  # 1.99 stalled cycles per insn (83.34%)
       406,168,187 branches # 20.302 M/sec (83.32%)
         6,869,950 branch-misses # 1.69% of all branches (83.32%)

       5.003164377 seconds time elapsed 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您可以看到在 5 秒钟内发生了多少次上下文切换、迁移、页面错误和其他事件，以及一些简单的计算。事实上，`perf`工具突出了您应该担心统计数据。在我的例子中，它是一个停滞周期前端/后端。此计数器显示由于某些外部原因(如等待内存访问)导致 CPU 管道停止(即未推进)的时间。

除了`perf stat`之外，你还有`perf top`——一个类似于`top`的工具，但是它是以符号方式工作的。

```
# perf top -a --stdio

PerfTop: 361 irqs/sec kernel:35.5% exact: 0.0% [4000Hz cycles], (all, 4 CPUs)
---------------------------------------------------------------------------------------------

 2.06% libglib-2.0.so.0.4400.1 [.] g_mutex_lock                   
 1.99% libglib-2.0.so.0.4400.1 [.] g_mutex_unlock                 
 1.47% [kernel] [k] __fget                         
 1.34% libpython2.7.so.1.0 [.] PyEval_EvalFrameEx             
 1.07% [kernel] [k] copy_user_generic_string       
 1.00% libpthread-2.21.so [.] pthread_mutex_lock             
 0.96% libpthread-2.21.so [.] pthread_mutex_unlock           
 0.85% libc-2.21.so [.] _int_malloc                    
 0.83% libpython2.7.so.1.0 [.] PyParser_AddToken              
 0.82% [kernel] [k] do_sys_poll                    
 0.81% libQtCore.so.4.8.6 [.] QMetaObject::activate          
 0.77% [kernel] [k] fput                           
 0.76% [kernel] [k] __audit_syscall_exit           
 0.75% [kernel] [k] unix_stream_recvmsg            
 0.63% [kernel] [k] ia32_sysenter_target 
```

Enter fullscreen mode Exit fullscreen mode

这里可以看到内核函数、glib 库函数、CPython 函数、Qt 框架函数和一个 pthread 函数结合其开销。这是一个很好的工具来查看系统状态，看看发生了什么。

### 应用剖析

要分析特定的应用程序，不管它是否已经运行，您可以使用`perf
record`来收集事件，然后使用`perf report`来分析程序行为。让我们看看:

```
# perf record -bag updatedb
[perf record: Woken up 259 times to write data]
[perf record: Captured and wrote 65.351 MB perf.data (127127 samples)] 
```

Enter fullscreen mode Exit fullscreen mode

现在用`perf report` :
深入研究数据

```
# perf report 
```

Enter fullscreen mode Exit fullscreen mode

你会看到一个漂亮的交互式 TUI 界面。

[![perf tui](img/85ee42b5b754f02fc7b3ce4ad1d871a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cD8k8DZF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-tui.png)

你可以放大 pid/thread

[![perf zoom into thread](img/4db2eba7afdbc250fbba4cb4215c9fee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BxWU06fA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-zoom-into-thread.png)

看看那里发生了什么

[![perf thread info](img/f69d33bdca1265434a1dcc8e21634a93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Fm7tCov--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-thread-info.png)

你可以查看漂亮的汇编代码(这看起来就像在 [radare](http://www.radare.org/r/) 中一样)

[![perf assembly](img/0bfa5537cadb443bbc4bcb4ceac75e51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TaTw_4Cx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-assembly.png)

并在其上运行脚本以查看函数调用直方图:

[![perf histogram](img/2fe2a50c886e7467ea8bf7973ba79cac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HGhq7OZp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-histogram.png)

如果对你来说还不够，那么`perf record`和`perf report`都有很多选项，所以尽情玩吧。

### 其他

除此之外，您还可以找到工具来分析内核内存子系统、锁定、KVM 客户机、调度、进行基准测试，甚至创建时间表。

为了便于说明，我将简要介绍我的简单的 [block_hasher](https://github.com/dzeban/block_hasher) 实用程序。之前，我已经用 [gprof 和 gcov](https://dev.to/blog/gprof-gcov/) 、 [Valgrind](https://dev.to/blog/valgrind/) 和 [ftrace](https://dev.to/blog/ftrace/) 对其进行过剖析。

## 热点剖析

当我用 [gprof 和 gcov](https://dev.to/blog/gprof-gcov/) 分析我的 [block_hasher](https://github.com/dzeban/block_hasher) util 时，我没有看到任何与应用程序代码相关的特殊情况，所以我认为不是应用程序代码导致了它的缓慢。让我们看看`perf`是否能帮助我们。

从`perf stat`开始，为 CPU ("-dac")
提供详细和缩放计数器的选项

```
# perf stat -dac ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000

 Performance counter stats for 'system wide':

      32978.276562 task-clock (msec) # 4.000 CPUs utilized (100.00%)
             6,349 context-switches # 0.193 K/sec (100.00%)
               142 cpu-migrations # 0.004 K/sec (100.00%)
             2,709 page-faults # 0.082 K/sec                  
    20,998,366,508 cycles # 0.637 GHz (41.08%)
    23,007,780,670 stalled-cycles-frontend # 109.57% frontend cycles idle (37.50%)
    18,687,140,923 stalled-cycles-backend # 88.99% backend cycles idle (42.64%)
    23,466,705,987 instructions # 1.12 insns per cycle        
                                                  # 0.98 stalled cycles per insn (53.74%)
     4,389,207,421 branches # 133.094 M/sec (55.51%)
        11,086,505 branch-misses # 0.25% of all branches (55.53%)
     7,435,101,164 L1-dcache-loads # 225.455 M/sec (37.50%)
       248,499,989 L1-dcache-load-misses # 3.34% of all L1-dcache hits (26.52%)
       111,621,984 LLC-loads # 3.385 M/sec (28.77%)
   <not supported> LLC-load-misses:HG       

       8.245518548 seconds time elapsed 
```

Enter fullscreen mode Exit fullscreen mode

嗯，没什么可疑的。6K 页面上下文切换是可以的，因为我的机器是双核的，我运行 10 个线程。2K 页面错误是正常的，因为我们正在从磁盘读取大量数据。大停滞周期前端/后端在这里是一种异常值，因为它在简单的`ls`和`--per-core`统计中仍然很大，显示 0.00%的停滞周期。

让我们收集简介:

```
# perf record -a -g -s -d -b ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000
[perf record: Woken up 73 times to write data]
[perf record: Captured and wrote 20.991 MB perf.data (33653 samples)] 
```

Enter fullscreen mode Exit fullscreen mode

选项包括:

*   -a 适用于所有 CPU
*   -g 表示调用图
*   -s 表示每线程计数
*   -d 代表采样地址。这个不确定，但不影响侧写
*   -b 用于采样任何采集的树枝

现在给我看看简介:

```
# perf report -g -T 
```

Enter fullscreen mode Exit fullscreen mode

[![perf report of block_hasher](img/221d2b058f3bf68f44e31fe536c51b82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--leI9BzOp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-report-block-hasher.png)

没什么。我研究了 block_hasher 线程，构建了一个直方图，查找了 vmlinux DSO，找到了开销最大的指令

[![perf __lock_acquire](img/04b06441fc222d6cc7aba2ffd0cc1c3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LCoVhIhk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-lock-acquire-assembly.png)

但还是不能说我找到了问题所在。那是因为没有真正的开销，没有什么是白转的。有些事情只是简单的睡觉。

我们之前在 ftrace 部分中所做的是热点分析，即我们试图在我们的应用或系统中找到导致 CPU 在无用周期中旋转的地方。通常，这是你想要的，但不是今天。我们需要了解`pread`为什么在睡觉。这就是我所说的“延迟剖析”。

## 延迟剖析

### 记录 sched_stat 和 sched_switch 事件

当你搜索 perf 文档时，首先找到的是[“Perf 教程”](https://perf.wiki.kernel.org/index.php/Tutorial)。“性能教程”页面几乎完全致力于“热点”场景，但是，幸运的是，有一个“其他场景”部分，包含[“剖析睡眠时间”](https://perf.wiki.kernel.org/index.php/Tutorial#Other_Scenarios)教程。

> 剖析睡眠时间
> 
> 这个特性显示了一个程序在哪里睡眠或者等待了多长时间。

哇，这就是我们需要的！

遗憾的是，默认情况下，计划统计数据分析不起作用。`perf inject`因
而失败

```
# perf inject -v -s -i perf.data.raw -o perf.data
registering plugin: /usr/lib64/traceevent/plugins/plugin_kmem.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_mac80211.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_function.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_hrtimer.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_sched_switch.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_jbd2.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_cfg80211.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_scsi.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_xen.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_kvm.so
overriding event (263) sched:sched_switch with new print handler
build id event received for [kernel.kallsyms]:
8adbfad59810c80cb47189726415682e0734788a
failed to write feature 2 
```

Enter fullscreen mode Exit fullscreen mode

原因是它在 build-id 缓存中找不到 scheduling stats 符号，因为 CONFIG_SCHEDSTATS 被禁用，因为它引入了一些“对上下文切换的重要性能影响”。红帽 bugzilla [Bug 1026506](https://bugzilla.redhat.com/show_bug.cgi?id=1026506) 和 [Bug 1013225](https://bugzilla.redhat.com/show_bug.cgi?id=1013225) 中的细节。Debian 内核也不支持这个选项。

你可以重新编译内核，启用`make
menuconfig`中的“收集调度程序统计”，但是快乐的 Fedora 用户可以只安装[调试内核](http://pkgs.fedoraproject.org/cgit/kernel.git/commit/?id=73e4f49352c74eeb2d0b951c47adf0b53278f84b) :

```
dnf install kernel-debug kernel-debug-devel kernel-debug-debuginfo 
```

Enter fullscreen mode Exit fullscreen mode

现在，当一切正常时，我们可以试一试:

```
# perf record -e sched:sched_stat_sleep -e sched:sched_switch -e sched:sched_process_exit -g -o perf.data.raw ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000
[perf record: Woken up 1 times to write data]
[perf record: Captured and wrote 0.564 MB perf.data.raw (2001 samples)]

# perf inject -v -s -i perf.data.raw -o perf.data.sched
registering plugin: /usr/lib64/traceevent/plugins/plugin_kmem.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_mac80211.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_function.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_hrtimer.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_sched_switch.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_jbd2.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_cfg80211.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_scsi.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_xen.so
registering plugin: /usr/lib64/traceevent/plugins/plugin_kvm.so
overriding event (266) sched:sched_switch with new print handler
build id event received for /usr/lib/debug/lib/modules/4.1.6-200.fc22.x86_64+debug/vmlinux: c6e34bcb0ab7d65e44644ea2263e89a07744bf85
Using /root/.debug/.build-id/c6/e34bcb0ab7d65e44644ea2263e89a07744bf85 for symbols 
```

Enter fullscreen mode Exit fullscreen mode

但是真的很失望，我已经展开了所有的调用链什么都没看到:

```
# perf report --show-total-period -i perf.data.sched
Samples: 10 of event 'sched:sched_switch', Event count (approx.): 31403254575
  Children Self Period Command Shared Object Symbol                                      
- 100.00% 0.00% 0 block_hasher libpthread-2.21.so [.] pthread_join                            
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] system_call                             
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] sys_futex                               
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] do_futex                                
     do_futex                                                                                                                         
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] futex_wait                              
     futex_wait                                                                                                                       
     do_futex                                                                                                                         
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] futex_wait_queue_me                     
     futex_wait_queue_me                                                                                                              
     futex_wait                                                                                                                       
     do_futex                                                                                                                         
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 0.00% 0 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] schedule                                
     schedule                                                                                                                         
     futex_wait_queue_me                                                                                                              
     futex_wait                                                                                                                       
     do_futex                                                                                                                         
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 100.00% 100.00% 31403254575 block_hasher e34bcb0ab7d65e44644ea2263e89a07744bf85 [k] __schedule                              
     __schedule                                                                                                                       
     schedule                                                                                                                         
     futex_wait_queue_me                                                                                                              
     futex_wait                                                                                                                       
     do_futex                                                                                                                         
     sys_futex                                                                                                                        
     system_call                                                                                                                      
   - pthread_join                                                                                                                     
        0                                                                                                                             
- 14.52% 0.00% 0 block_hasher [unknown] [.] 0000000000000000                        
     0 
```

Enter fullscreen mode Exit fullscreen mode

### 性能计划

让我们看看我们还能做什么。有一个`perf sched`命令，它的`latency`子命令用于“报告每个任务的调度延迟和工作负载的其他调度属性”。为什么不试试呢？

```
# perf sched record -o perf.sched -g ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000
[perf record: Woken up 6 times to write data]
[perf record: Captured and wrote 13.998 MB perf.sched (56914 samples)]

# perf report -i perf.sched 
```

Enter fullscreen mode Exit fullscreen mode

我已经检查了`sched_switch`和`sched_stat_runtime`事件的样品(分别为 15K 和 17K ),没有发现任何问题。但是后来我看了看`sched_stat_iowait`。

[![perf sched_stat_iowait](img/32f5aec258e69273447feee3f649d249.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tyxtmqr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-sched-stat-iowait.png)

在那里我发现了一件非常可疑的事情:

[![perf dm-delay](img/798ee2d5753f2bbe7bf0fd44f90ed79b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CQPP3IyY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/perf-dm-delay.png)

看到了吗？几乎所有的符号都来自“kernel.vmlinux”共享对象，但是有一个奇怪的名字“0x 0000000005 F8 CCC 27”来自“dm_delay”对象。等等，“dm_delay”是什么？快速查找给了我们答案:

```
> dm-delay
> ========
>
> Device-Mapper's "delay" target delays reads and/or writes
> and maps them to different devices. 
```

Enter fullscreen mode Exit fullscreen mode

什么？！延迟读取和/或写入？真的吗？

```
# dmsetup info 
Name: delayed
State: ACTIVE
Read Ahead: 256
Tables present: LIVE
Open count: 1
Event number: 0
Major, minor: 253, 0
Number of targets: 1

# dmsetup table
delayed: 0 1000000 delay 1:7 0 30

# udevadm info -rq name /sys/dev/block/1:7
/dev/ram7 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们将块设备“/dev/ram7”映射到设备映射程序“延迟”目标，将 I/O 请求延迟到 30 毫秒。这就是为什么整个 RAID 很慢的原因——RAID 0 的性能就是 RAID 中最慢的磁盘的性能。

当然，我一开始就知道。我只是想看看我能否用分析工具检测到它。在这种情况下，我认为说`perf`有所帮助是不公平的。其实，`perf`在界面上给了很多困惑。看上图。这几十行“99.67%”告诉我们什么？以下哪些符号会导致延迟？如何解读？如果我不是真的专注，比如在几个小时的调试和调查之后，我不可能注意到它。如果我发出神奇的`perf inject`命令，它将崩溃`sched_stat_iowait`命令，我将不会在顶级记录中看到 dm-delay。

再说一次，这一切都很令人困惑，我注意到这一点真是幸运。

## 结论

Perf 是一个真正通用且极其复杂的工具，只需要很少的文档。在一些简单的情况下，会对你有很大的帮助。但是离主流问题只有几步之遥，你就只剩下不直观的数据了。我们都需要关于性能的各种文档，包括教程、书籍、幻灯片、视频，这些文档不仅要触及它的表面，还要给出它如何工作、能做什么和不能做什么的全面概述。我希望我的这篇文章有助于实现这个目标(我花了半年时间来写这篇文章)。

## 参考文献

1.  [性能教程](https://perf.wiki.kernel.org/index.php/Tutorial)
2.  [文斯·韦弗的绩效页面](http://web.eece.maine.edu/~vweaver/projects/perf_events)
3.  [美丽的布兰登·格雷格的“业绩”页面](http://www.brendangregg.com/perf.html)