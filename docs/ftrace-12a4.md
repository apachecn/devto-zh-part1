# Ftrace

> 原文：<https://dev.to/dzeban/ftrace-12a4>

## ftace

**Ftrace** 是一个跟踪和分析 Linux 内核的框架，具有以下特性:

*   核函数跟踪
*   调用图跟踪
*   跟踪点支持
*   通过 kprobes 进行动态跟踪
*   内核函数的统计信息
*   内核事件的统计数据

本质上， *ftrace* 围绕智能无锁环形缓冲区实现而构建(参见[Documentation/trace/ring-buffer-design . txt/](http://lxr.free-electrons.com/source/Documentation/trace/ring-buffer-design.txt?v=3.15))。该缓冲区存储所有 *ftrace* 信息，并通过`/sys/kernel/debug/tracing/`中的 debugfs <sup id="fnref:debugfs">[1](#fn:debugfs)</sup> 导入。所有操作都是在这个目录中通过简单的文件操作完成的。

## ftrace 如何工作

正如我刚才所说的， *ftrace* 是一个框架，这意味着它只提供环形缓冲区，所有真正的工作都由所谓的**追踪器**完成。目前， *ftrace* 包括下一个示踪剂:

*   *功能*–默认跟踪器；
*   *function _ graph*–构造调用图；
*   *irqsoff* 、*pre impoff*、 *preemptirqsoff* 、 *wakeup* 、*wake up _ rt*–延迟跟踪器。这些是 *ftrace* 的起源，它们出现在-rt 内核中。我不会给你很多关于这个话题的信息，因为它更多的是关于实时，调度和硬件的东西；
*   *nop*——你猜。

此外，作为附加功能，您将获得:

*   内核跟踪点支持；
*   kprobes 支持；
*   blktrace 支持，虽然要被删除了。

现在让我们看看具体的示踪剂。

### 功能追踪

主要的 *ftrace* 函数是，嗯，函数追踪(`function`和`function_graph`追踪器)。为了实现这一点，内核函数使用了`mcount`调用，就像使用 [gprof](https://dev.to/blog/gprof-gcov/) 一样。但是内核`mcount`当然完全不同于用户空间，因为它依赖于架构。这种依赖性是构建调用图所必需的，更具体地说是从先前的堆栈帧中获取调用者地址所必需的。

这个`mcount`被插入到函数序言中，如果它被关闭，它什么也不做。但是如果它被打开，那么它将调用 *ftrace* 函数，该函数根据当前的跟踪器将不同的数据写入环形缓冲区。

### 事件追踪

借助[跟踪点](http://lxr.free-electrons.com/source/Documentation/trace/events.txt?v=3.15)完成事件跟踪。您通过`/sys/kernel/debug/tracing`中的`set_event`文件设置事件，然后它将在环形缓冲区中被跟踪。例如，要追踪`kmalloc`，只需发出

```
echo kmalloc > /sys/kernel/debug/tracing/set_event 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以看到`trace` :

```
tail-7747 [000] .... 12584.876544: kmalloc: call_site=c06c56da ptr=e9cf9eb0 bytes_req=4 bytes_alloc=8 gfp_flags=GFP_KERNEL|GFP_ZERO 
```

Enter fullscreen mode Exit fullscreen mode

它和`include/trace/events/kmem.h`中的一样，意味着它只是一个*跟踪点*。

### k 探针追踪

在内核 3.10 中，增加了对 *ftrace* 的 [kprobes 和 kretprobes](http://lwn.net/Articles/343766/) 的支持。现在，您无需编写自己的内核模块就可以进行动态跟踪。但是，不幸的是，这并没有太大的关系，只是

*   寄存器值
*   内存转储
*   符号值
*   堆栈值
*   返回值(kretprobes)

同样，这个输出被写入环形缓冲区。此外，你可以计算一些统计数据。

让我们跟踪一些没有 tracepoint 的东西，比如不是来自内核而是来自内核模块的东西。

在我的三星 N210 笔记本电脑上，我有一个很可能没有任何痕迹的 WiFi 模块。来检查一下这个刚刚 grep *可用 _ 事件* :

```
[tracing]# grep ath available_events 
cfg80211:rdev_del_mpath
cfg80211:rdev_add_mpath
cfg80211:rdev_change_mpath
cfg80211:rdev_get_mpath
cfg80211:rdev_dump_mpath
cfg80211:rdev_return_int_mpath_info
ext4:ext4_ext_convert_to_initialized_fastpath 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我们可以在哪些函数上放置探针:

```
[tracing]# grep "\[ath9k\]" /proc/kallsyms | grep ' t ' | grep rx
f82e6ed0 t ath_rx_remove_buffer [ath9k]
f82e6f60 t ath_rx_buf_link.isra.25 [ath9k]
f82e6ff0 t ath_get_next_rx_buf [ath9k]
f82e7130 t ath_rx_edma_buf_link [ath9k]
f82e7200 t ath_rx_addbuffer_edma [ath9k]
f82e7250 t ath_rx_edma_cleanup [ath9k]
f82f3720 t ath_debug_stat_rx [ath9k]
f82e7a70 t ath_rx_tasklet [ath9k]
f82e7310 t ath_rx_cleanup [ath9k]
f82e7800 t ath_calcrxfilter [ath9k]
f82e73e0 t ath_rx_init [ath9k] 
```

Enter fullscreen mode Exit fullscreen mode

(第一个 grep 过滤来自 *ath9k* 模块的符号，第二个 grep 过滤位于文本部分的函数，最后一个 grep 过滤接收器函数)。

例如，我们将追溯 [`ath_get_next_rx_buf`](http://lxr.free-electrons.com/source/drivers/net/wireless/ath/ath9k/recv.c#L678) 函数。

```
[tracing]# echo 'r:ath_probe ath9k:ath_get_next_rx_buf $retval' >> kprobe_events 
```

Enter fullscreen mode Exit fullscreen mode

这个命令不是我想出来的——查看 Documentation/tracing/kprobetrace . txt

这将 retprobe 放在我们的函数上，并获取返回值(它只是一个指针)。

放置探针后，我们必须启用它:

```
[tracing]# echo 1 > events/kprobes/enable 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在`trace`文件中寻找输出，这里是:

```
midori-6741 [000] d.s. 3011.304724: ath_probe: (ath_rx_tasklet+0x35a/0xc30 [ath9k] <- ath_get_next_rx_buf) arg1=0xf6ae39f4 
```

Enter fullscreen mode Exit fullscreen mode

## 示例(block_hasher)

默认情况下， *ftrace* 正在收集所有内核函数的信息，这非常庞大。但是，作为一个复杂的内核机制， *ftrace* 有许多特性、多种选项、可调参数等等，我不想谈论这些，因为有大量关于 lwn 的手册和文章(参见阅读部分)。因此，难怪我们可以通过 PID 进行过滤。以下是脚本:

```
#!/bin/sh

DEBUGFS=`grep debugfs /proc/mounts | awk '{ print $2; }'`

# Reset trace stat
echo 0 > $DEBUGFS/tracing/function_profile_enabled
echo 1 > $DEBUGFS/tracing/function_profile_enabled

echo $$ > $DEBUGFS/tracing/set_ftrace_pid
echo function > $DEBUGFS/tracing/current_tracer

exec $* 
```

Enter fullscreen mode Exit fullscreen mode

`function_profile_enabled`配置收集统计信息。

启动我们的魔法脚本

```
./ftrace-me ./block_hasher -d /dev/md127 -b 1048576 -t10 -n10000 
```

Enter fullscreen mode Exit fullscreen mode

从`tracing/trace_stat/`
中的文件获取每个处理器的统计数据

```
head -n50 tracing/trace_stat/function* > ~/trace_stat 
```

Enter fullscreen mode Exit fullscreen mode

且看 top 5

```
==> function0 <==
  Function Hit Time Avg
  -------- --- ---- ---
  schedule 444425 8653900277 us 19472.12 us 
  schedule_timeout 36019 813403521 us 22582.62 us 
  do_IRQ 8161576 796860573 us 97.635 us
  do_softirq 486268 791706643 us 1628.128 us 
  __do_softirq 486251 790968923 us 1626.667 us 

==> function1 <==
  Function Hit Time Avg
  -------- --- ---- ---
  schedule 1352233 13378644495 us 9893.742 us 
  schedule_hrtimeout_range 11853 2708879282 us 228539.5 us 
  poll_schedule_timeout 7733 2366753802 us 306058.9 us 
  schedule_timeout 176343 1857637026 us 10534.22 us 
  schedule_timeout_interruptible 95 1637633935 us 17238251 us 

==> function2 <==
  Function Hit Time Avg
  -------- --- ---- ---
  schedule 1260239 9324003483 us 7398.599 us 
  vfs_read 215859 884716012 us 4098.582 us 
  do_sync_read 214950 851281498 us 3960.369 us 
  sys_pread64 13136 830103896 us 63193.04 us 
  generic_file_aio_read 14955 830034649 us 55502.14 us 
```

Enter fullscreen mode Exit fullscreen mode

(不用关注`schedule`——只是调度器的调用)。

大部分时间我们都在`schedule`、`do_IRQ`、`schedule_hrimeout_range`和`vfs_read`中度过，这意味着我们要么等待阅读，要么等待超时。这就奇怪了！为了更清楚，我们可以禁用所谓的图形时间，这样就不会计算子函数。让我解释一下，默认情况下 *ftrace* 将函数时间计算为函数本身加上所有子程序调用的时间。那就是和 *ftrace* 中的`graph_time`选项。

告诉

```
echo 0 > options/graph_time 
```

Enter fullscreen mode Exit fullscreen mode

并再次收集配置文件

```
==> function0 <==
  Function Hit Time Avg
  -------- --- ---- ---
  schedule 34129 6762529800 us 198146.1 us 
  mwait_idle 50428 235821243 us 4676.394 us 
  mempool_free 59292718 27764202 us 0.468 us    
  mempool_free_slab 59292717 26628794 us 0.449 us    
  bio_endio 49761249 24374630 us 0.489 us    

==> function1 <==
  Function Hit Time Avg
  -------- --- ---- ---
  schedule 958708 9075670846 us 9466.564 us 
  mwait_idle 406700 391923605 us 963.667 us  
  _spin_lock_irq 22164884 15064205 us 0.679 us    
  __make_request 3890969 14825567 us 3.810 us    
  get_page_from_freelist 7165243 14063386 us 1.962 us 
```

Enter fullscreen mode Exit fullscreen mode

现在我们看到有人莫名其妙地打电话来，这很有趣。我们不能说它是如何发生的。

也许，我们应该得到一个函数图！我们知道一切都是从`pread`开始的，所以让我们试着从`pread`开始追踪函数调用。

到那时，我已经厌倦了读/写 debugfs 文件，开始使用 CLI 接口来 *ftrace* ，也就是 [`trace-cmd`](http://git.kernel.org/cgit/linux/kernel/git/rostedt/trace-cmd.git) 。

使用`trace-cmd`非常简单——首先，你用`trace-cmd
record`记录，然后用`trace-cmd report`分析。

记录:

```
trace-cmd record -p function_graph -o graph_pread.dat -g sys_pread64 \
        ./block_hasher -d /dev/md127 -b 1048576 -t10 -n100 
```

Enter fullscreen mode Exit fullscreen mode

看:

```
trace-cmd report -i graph_pread.dat | less 
```

Enter fullscreen mode Exit fullscreen mode

令人失望。

```
block_hasher-4102 [001] 2764.516562: funcgraph_entry: | __page_cache_alloc() {
block_hasher-4102 [001] 2764.516562: funcgraph_entry: | alloc_pages_current() {
block_hasher-4102 [001] 2764.516562: funcgraph_entry: 0.052 us | policy_nodemask();
block_hasher-4102 [001] 2764.516563: funcgraph_entry: 0.058 us | policy_zonelist();
block_hasher-4102 [001] 2764.516563: funcgraph_entry: | __alloc_pages_nodemask() {
block_hasher-4102 [001] 2764.516564: funcgraph_entry: 0.054 us | _cond_resched();
block_hasher-4102 [001] 2764.516564: funcgraph_entry: 0.063 us | next_zones_zonelist();
block_hasher-4109 [000] 2764.516564: funcgraph_entry: | SyS_pread64() {
block_hasher-4102 [001] 2764.516564: funcgraph_entry: | get_page_from_freelist() {
block_hasher-4109 [000] 2764.516564: funcgraph_entry: | __fdget() {
block_hasher-4102 [001] 2764.516565: funcgraph_entry: 0.052 us | next_zones_zonelist();
block_hasher-4109 [000] 2764.516565: funcgraph_entry: | __fget_light() {
block_hasher-4109 [000] 2764.516565: funcgraph_entry: 0.217 us | __fget();
block_hasher-4102 [001] 2764.516565: funcgraph_entry: 0.046 us | __zone_watermark_ok();
block_hasher-4102 [001] 2764.516566: funcgraph_entry: 0.057 us | __mod_zone_page_state();
block_hasher-4109 [000] 2764.516566: funcgraph_exit: 0.745 us | }
block_hasher-4109 [000] 2764.516566: funcgraph_exit: 1.229 us | }
block_hasher-4102 [001] 2764.516566: funcgraph_entry: | zone_statistics() {
block_hasher-4109 [000] 2764.516566: funcgraph_entry: | vfs_read() {
block_hasher-4102 [001] 2764.516566: funcgraph_entry: 0.064 us | __inc_zone_state();
block_hasher-4109 [000] 2764.516566: funcgraph_entry: | rw_verify_area() {
block_hasher-4109 [000] 2764.516567: funcgraph_entry: | security_file_permission() {
block_hasher-4102 [001] 2764.516567: funcgraph_entry: 0.057 us | __inc_zone_state();
block_hasher-4109 [000] 2764.516567: funcgraph_entry: 0.048 us | cap_file_permission();
block_hasher-4102 [001] 2764.516567: funcgraph_exit: 0.907 us | }
block_hasher-4102 [001] 2764.516567: funcgraph_entry: 0.056 us | bad_range();
block_hasher-4109 [000] 2764.516567: funcgraph_entry: 0.115 us | __fsnotify_parent();
block_hasher-4109 [000] 2764.516568: funcgraph_entry: 0.159 us | fsnotify();
block_hasher-4102 [001] 2764.516568: funcgraph_entry: | mem_cgroup_bad_page_check() {
block_hasher-4102 [001] 2764.516568: funcgraph_entry: | lookup_page_cgroup_used() {
block_hasher-4102 [001] 2764.516568: funcgraph_entry: 0.052 us | lookup_page_cgroup();
block_hasher-4109 [000] 2764.516569: funcgraph_exit: 1.958 us | }
block_hasher-4102 [001] 2764.516569: funcgraph_exit: 0.435 us | }
block_hasher-4109 [000] 2764.516569: funcgraph_exit: 2.487 us | }
block_hasher-4102 [001] 2764.516569: funcgraph_exit: 0.813 us | }
block_hasher-4102 [001] 2764.516569: funcgraph_exit: 4.666 us | } 
```

Enter fullscreen mode Exit fullscreen mode

首先，没有直接的函数调用链，它不断地被中断并转移到另一个 CPU。其次，有很多噪音，例如`inc_zone_state`和`__page_cache_alloc`呼叫。最后，既没有*MDR id*函数，也没有`mwait_idle`调用！

原因是 *ftrace* 默认源代码(跟踪点)和内核代码的异步/回调特性。你不会看到来自`sys_pread64`的直接函数调用链，内核不会这样工作。

但是如果我们在 mdraid 函数上设置 kprobes 呢？没问题！只需添加`mwait_idle`和`md_make_request`的返回探头:

```
# echo 'r:md_make_request_probe md_make_request $retval' >> kprobe_events 
# echo 'r:mwait_probe mwait_idle $retval' >> kprobe_events 
```

Enter fullscreen mode Exit fullscreen mode

用`trace-cmd`重复该程序，得到函数图:

```
# trace-cmd record -p function_graph -o graph_md.dat -g md_make_request -e md_make_request_probe -e mwait_probe -F \
            ./block_hasher -d /dev/md0 -b 1048576 -t10 -n100 
```

Enter fullscreen mode Exit fullscreen mode

`-e`启用特定事件。现在，看函数图:

```
block_hasher-28990 [000] 10235.125319: funcgraph_entry: | md_make_request() {
block_hasher-28990 [000] 10235.125321: funcgraph_entry: | make_request() {
block_hasher-28990 [000] 10235.125322: funcgraph_entry: 0.367 us | md_write_start();
block_hasher-28990 [000] 10235.125323: funcgraph_entry: | bio_clone_mddev() {
block_hasher-28990 [000] 10235.125323: funcgraph_entry: | bio_alloc_bioset() {
block_hasher-28990 [000] 10235.125323: funcgraph_entry: | mempool_alloc() {
block_hasher-28990 [000] 10235.125323: funcgraph_entry: 0.178 us | _cond_resched();
block_hasher-28990 [000] 10235.125324: funcgraph_entry: | mempool_alloc_slab() {
block_hasher-28990 [000] 10235.125324: funcgraph_entry: | kmem_cache_alloc() {
block_hasher-28990 [000] 10235.125324: funcgraph_entry: | cache_alloc_refill() {
block_hasher-28990 [000] 10235.125325: funcgraph_entry: 0.275 us | _spin_lock();
block_hasher-28990 [000] 10235.125326: funcgraph_exit: 1.072 us | }
block_hasher-28990 [000] 10235.125326: funcgraph_exit: 1.721 us | }
block_hasher-28990 [000] 10235.125326: funcgraph_exit: 2.085 us | }
block_hasher-28990 [000] 10235.125326: funcgraph_exit: 2.865 us | }
block_hasher-28990 [000] 10235.125326: funcgraph_entry: 0.187 us | bio_init();
block_hasher-28990 [000] 10235.125327: funcgraph_exit: 3.665 us | }
block_hasher-28990 [000] 10235.125327: funcgraph_entry: 0.229 us | __bio_clone();
block_hasher-28990 [000] 10235.125327: funcgraph_exit: 4.584 us | }
block_hasher-28990 [000] 10235.125328: funcgraph_entry: 1.093 us | raid5_compute_sector();
block_hasher-28990 [000] 10235.125330: funcgraph_entry: | blk_recount_segments() {
block_hasher-28990 [000] 10235.125330: funcgraph_entry: 0.340 us | __blk_recalc_rq_segments();
block_hasher-28990 [000] 10235.125331: funcgraph_exit: 0.769 us | }
block_hasher-28990 [000] 10235.125331: funcgraph_entry: 0.202 us | _spin_lock_irq();
block_hasher-28990 [000] 10235.125331: funcgraph_entry: 0.194 us | generic_make_request();
block_hasher-28990 [000] 10235.125332: funcgraph_exit: + 10.613 us | }
block_hasher-28990 [000] 10235.125332: funcgraph_exit: + 13.638 us | } 
```

Enter fullscreen mode Exit fullscreen mode

好多了！但是因为某些原因，它没有`mwait_idle`调用。而它只是调用`generic_make_request`。我已经尝试并记录了`generic_make_request` ( `-g`选项)的功能图。仍然没有运气。我提取了所有包含*等待*的函数，结果如下:

```
# grep 'wait' graph_md.graph | cut -f 2 -d'|' | awk '{print $1}' | sort -n | uniq -c
     18 add_wait_queue()
   2064 bit_waitqueue()
      1 bit_waitqueue();
   1194 finish_wait()
     28 page_waitqueue()
   2033 page_waitqueue();
   1222 prepare_to_wait()
     25 remove_wait_queue()
      4 update_stats_wait_end()
    213 update_stats_wait_end(); 
```

Enter fullscreen mode Exit fullscreen mode

(`cut`将分隔函数名，`awk`将只打印函数名，`uniq`用`sort`将减少函数名)

与超时无关。我已经尝试过 grep 等待*超时*，该死的，没有任何可疑之处。

所以，现在我要停下来，因为它不会去任何地方。

## 结论

嗯，真的很有趣！ftrace 是一个如此强大的工具，但它是为调试而设计的，而不是用来分析的。我能够得到内核函数调用图，得到源代码级别的内核执行统计数据(你能相信吗？)，追踪一些未知的函数，所有发生的事情都要感谢 *ftrace* 。祝福它！

## 阅读

*   使用 Ftrace - [第 1 部分](http://lwn.net/Articles/365835/)、[第 2 部分](http://lwn.net/Articles/366796/)调试内核
*   [Ftrace 函数跟踪器的秘密](http://lwn.net/Articles/370423/)
*   [T2`trace-cmd`](http://lwn.net/Articles/410200/)
*   [带 ftrace 的动态探头](http://lwn.net/Articles/343766/)
*   [Linux 内核中的动态事件跟踪](https://events.linuxfoundation.org/slides/lfcs2010_hiramatsu.pdf)

* * *

1.  debugfs 是这样挂载的:`mount -t debugfs none /sys/kernel/debug` <sup>【返回】</sup>