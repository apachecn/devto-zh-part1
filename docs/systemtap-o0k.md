# SystemTap

> 原文：<https://dev.to/dzeban/systemtap-o0k>

## SystemTap

SystemTap 是一个基于 kprobes 的分析和调试基础设施[。本质上，它是 kprobes 的一个脚本工具。它允许您动态地检测内核和用户应用程序，以跟踪系统行为中复杂和模糊的问题。](https://sourceware.org/systemtap/systemtap-ols.pdf)

使用 SystemTap，您可以用一种受 C、awk 和 dtrace 启发的特殊语言编写 tapscript。SystemTap 语言要求您为内核或用户空间中定义的探测器编写处理程序，当执行命中这些探测器时，将调用这些处理程序。您可以定义自己的函数，并使用丰富的 [tapsets](https://sourceware.org/systemtap/tapsets/) 库。语言为你提供整数、字符串、关联数组和统计数据，而不需要类型和内存分配。关于 SystemTap 语言的全面信息可以在[语言参考](https://sourceware.org/systemtap/langref/)中找到。

您编写的脚本是“精心制作的”(解析对 tapsets、内核和用户空间符号的引用)，被翻译成 C，用 kprobes API 调用包装，并编译成内核模块，最后被加载到内核中。

脚本输出和收集的其他数据通过 relayfs 或 netlink 等高性能传输从内核传输到用户空间。

## 设置

安装部分很无聊，取决于你的发行版，在 Fedora 上很简单:

```
$ dnf install systemtap 
```

Enter fullscreen mode Exit fullscreen mode

您将需要 SystemTap 运行时和客户端工具以及 tapsets 和其他开发文件来构建您的模块。

此外，您还需要内核调试信息:

```
$ dnf debuginfo-install kernel 
```

Enter fullscreen mode Exit fullscreen mode

安装后，您可以检查它是否工作:

```
$ stap -v -e 'probe begin { println("Started") }'
Pass 1: parsed user script and 592 library scripts using 922624virt/723440res/7456shr/715972data kb, in 3250usr/220sys/3577real ms.
Pass 2: analyzed script: 1 probe, 0 functions, 0 embeds, 0 globals using 963940virt/765008res/7588shr/757288data kb, in 320usr/10sys/338real ms.
Pass 3: translated to C into "/tmp/stapMS0u1v/stap_804234031353467eccd1a028c78ff3e3_819_src.c" using 963940virt/765008res/7588shr/757288data kb, in 0usr/0sys/0real ms.
Pass 4: compiled C into "stap_804234031353467eccd1a028c78ff3e3_819.ko" in 9530usr/1380sys/11135real ms.
Pass 5: starting run.
Started
^CPass 5: run completed in 20usr/20sys/45874real ms. 
```

Enter fullscreen mode Exit fullscreen mode

## 游乐场

SystemTap 能做什么的各种例子可以在[这里](https://sourceware.org/systemtap/examples/keyword-index.html)找到。

你可以看到带有 [para-callgraph.stp](https://sourceware.org/systemtap/examples/general/para-callgraph.stp) :
的调用图

```
$ stap para-callgraph.stp 'process("/home/avd/dev/block_hasher/block_hasher").function("*")' \
  -c '/home/avd/dev/block_hasher/block_hasher -d /dev/md0 -b 1048576 -t 10 -n 10000'

     0 block_hasher(10792):->_start 
    11 block_hasher(10792): ->__libc_csu_init 
    14 block_hasher(10792): ->_init 
    17 block_hasher(10792): <-_init 
    18 block_hasher(10792): ->frame_dummy 
    21 block_hasher(10792): ->register_tm_clones 
    23 block_hasher(10792): <-register_tm_clones 
    25 block_hasher(10792): <-frame_dummy 
    26 block_hasher(10792): <-__libc_csu_init 
    31 block_hasher(10792): ->main argc=0x9 argv=0x7ffc78849278
    44 block_hasher(10792): ->bdev_open dev_path=0x7ffc78849130
    88 block_hasher(10792): <-bdev_open return=0x163b010
     0 block_hasher(10796):->thread_func arg=0x163b2c8
     0 block_hasher(10797):->thread_func arg=0x163b330
     0 block_hasher(10795):->thread_func arg=0x163b260
     0 block_hasher(10798):->thread_func arg=0x163b398
     0 block_hasher(10799):->thread_func arg=0x163b400
     0 block_hasher(10800):->thread_func arg=0x163b468
     0 block_hasher(10801):->thread_func arg=0x163b4d0
     0 block_hasher(10802):->thread_func arg=0x163b538
     0 block_hasher(10803):->thread_func arg=0x163b5a0
     0 block_hasher(10804):->thread_func arg=0x163b608
407360 block_hasher(10799): ->time_diff start={...} end={...}
407371 block_hasher(10799): <-time_diff 
407559 block_hasher(10799):<-thread_func return=0x0
436757 block_hasher(10795): ->time_diff start={...} end={...}
436765 block_hasher(10795): <-time_diff 
436872 block_hasher(10795):<-thread_func return=0x0
489156 block_hasher(10797): ->time_diff start={...} end={...}
489163 block_hasher(10797): <-time_diff 
489277 block_hasher(10797):<-thread_func return=0x0
506616 block_hasher(10803): ->time_diff start={...} end={...}
506628 block_hasher(10803): <-time_diff 
506754 block_hasher(10803):<-thread_func return=0x0
526005 block_hasher(10801): ->time_diff start={...} end={...}
526010 block_hasher(10801): <-time_diff 
526075 block_hasher(10801):<-thread_func return=0x0
9840716 block_hasher(10804): ->time_diff start={...} end={...}
9840723 block_hasher(10804): <-time_diff 
9840818 block_hasher(10804):<-thread_func return=0x0
9857787 block_hasher(10802): ->time_diff start={...} end={...}
9857792 block_hasher(10802): <-time_diff 
9857895 block_hasher(10802):<-thread_func return=0x0
9872655 block_hasher(10796): ->time_diff start={...} end={...}
9872664 block_hasher(10796): <-time_diff 
9872816 block_hasher(10796):<-thread_func return=0x0
9875681 block_hasher(10798): ->time_diff start={...} end={...}
9875686 block_hasher(10798): <-time_diff 
9874408 block_hasher(10800): ->time_diff start={...} end={...}
9874413 block_hasher(10800): <-time_diff 
9875767 block_hasher(10798):<-thread_func return=0x0
9874482 block_hasher(10800):<-thread_func return=0x0
9876305 block_hasher(10792): ->bdev_close dev=0x163b010
10180742 block_hasher(10792): <-bdev_close 
10180801 block_hasher(10792): <-main return=0x0
10180808 block_hasher(10792): ->__do_global_dtors_aux 
10180814 block_hasher(10792): ->deregister_tm_clones 
10180817 block_hasher(10792): <-deregister_tm_clones 
10180819 block_hasher(10792): <-__do_global_dtors_aux 
10180821 block_hasher(10792): ->_fini 
10180823 block_hasher(10792): <-_fini 
Pass 5: run completed in 20usr/3200sys/10716real ms. 
```

Enter fullscreen mode Exit fullscreen mode

你可以用 [latencytap.stp](https://sourceware.org/systemtap/examples/profiling/latencytap.stp) :
找到延迟的一般来源

```
$ stap -v latencytap.stp -c \
'/home/avd/dev/block_hasher/block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000000'

Reason Count Average(us) Maximum(us) Percent%
Reading from file 490 49311 53833 96%
Userspace lock contention 8 118734 929420 3%
Page fault 17 27 65 0%
unmapping memory 4 37 55 0%
mprotect() system call 6 25 45 0%
                                                      4 19 37 0%
                                                      3 23 49 0%
Page fault 2 24 46 0%
Page fault 2 20 36 0% 
```

Enter fullscreen mode Exit fullscreen mode

注意:您可能需要更改 latencytap.stp:
中的计时器间隔

```
-probe timer.s(30) {
+probe timer.s(5) { 
```

Enter fullscreen mode Exit fullscreen mode

甚至还有用 SystemTap 写的 [2048！](https://sourceware.org/systemtap/examples/stapgames/2048.stp)

[![SystemTap 2048](img/1beeb6a1fbe8687b34b42585780a7ff9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4uw2Qc56--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alex.dzyoba.com/img/stap-2048.png)

总而言之，简单方便。你可以在一天之内完成它！它如你所料地工作，这是一件大事，因为它给你确定性和信心，在薄弱的基础上剖析内核问题。

## 分析 block_hasher 的 I/O 延迟

那么，我们如何使用它来分析内核、模块或用户空间应用程序呢？问题是，我们手中几乎拥有无限的权力。我们可以想做什么就做什么，想怎么做就怎么做，但我们必须知道自己*想要什么*并用 SystemTap 语言表达出来。

您有一个 Tap sets——SystemTap 的标准库——它包含大量可供 tap 脚本使用的探针和函数。

但是，说实话，没有人想写脚本，每个人都想使用由具有专业知识并且已经花了很多时间调试和调整脚本的人编写的脚本。

让我们看看我们能在 [SystemTap I/O 示例](https://sourceware.org/systemtap/examples/keyword-index.html#IO)中找到什么。

有一个看似合法:[“ioblktime”](https://sourceware.org/systemtap/examples/io/ioblktime.stp)。让我们启动它:

```
stap -v ioblktime.stp -o ioblktime -c \
'/home/avd/dev/block_hasher/block_hasher -d /dev/md0 -b 1048576 -t 10 -n 10000' 
```

Enter fullscreen mode Exit fullscreen mode

这里是我们得到的:

```
device rw total (us) count avg (us)
  ram4 R 101628 981 103
  ram5 R 99328 981 101
  ram6 R 64973 974 66
  ram2 R 57002 974 58
  ram3 R 66635 974 68
  ram0 R 101806 974 104
  ram1 R 98470 974 101
  ram7 R 64250 974 65
  dm-0 R 48337401 974 49627
   sda W 3871495 376 10296
   sda R 125794 14 8985
device rw total (us) count avg (us)
   sda W 278560 18 15475 
```

Enter fullscreen mode Exit fullscreen mode

我们看到一个奇怪的设备 dm-0。快速检查:

```
$ dmsetup info /dev/dm-0 
Name: delayed
State: ACTIVE
Read Ahead: 256
Tables present: LIVE
Open count: 1
Event number: 0
Major, minor: 253, 0
Number of targets: 1 
```

Enter fullscreen mode Exit fullscreen mode

我们之前在看到的[设备映射器的“延迟”目标。该目标创建一个块设备，它同样映射到磁盘，但是将每个请求延迟给定的毫秒数。**这是我们 RAID 问题的一个原因:条带化 RAID 的性能是最慢磁盘的性能。**](https://dev.to/blog/perf/)

我还寻找了其他的例子，但是它们大多显示了哪个进程产生了最多的 I/O。

我们试着自己写剧本吧！

有一个 tapset 专用于 [I/O 调度程序和块 I/O](https://sourceware.org/systemtap/tapsets/iosched.stp.html) 。让我们使用`probe::ioblock.end`匹配我们的 RAID 设备并打印回溯。

```
probe ioblock.end
{
    if (devname == "md0") {
        printf("%s: %d\n", devname, sector);
        print_backtrace()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这是行不通的，因为 RAID 设备请求最终在具体的磁盘中结束，所以我们必须挂钩到`raid0`模块。

潜入 [`drivers/md/raid0.c`](http://lxr.free-electrons.com/source/drivers/md/raid0.c?v=4.2) 再看向 [`raid0_make_request`](http://lxr.free-electrons.com/source/drivers/md/raid0.c?v=4.2#L507) 。RAID 0 的核心代码如下:

```
530                 if (sectors < bio_sectors(bio)) {
531                         split = bio_split(bio, sectors, GFP_NOIO, fs_bio_set);
532                         bio_chain(split, bio);
533                 } else {
534                         split = bio;
535                 }
536
537                 zone = find_zone(mddev->private, &(sector));
538                 tmp_dev = map_sector(mddev, zone, sector, &(sector));
539                 split->bi_bdev = tmp_dev->bdev;
540                 split->bi_iter.bi_sector = sector + zone->dev_start +
541                         tmp_dev->data_offset;
                           ...
548                         generic_make_request(split); 
```

Enter fullscreen mode Exit fullscreen mode

这告诉我们:“将 bio 请求分割到 raid md 设备，将其映射到特定磁盘并发出`generic_make_request`”。

仔细看`generic_make_request`

```
1966         do {
1967                 struct request_queue *q = bdev_get_queue(bio->bi_bdev);
1968 
1969                 q->make_request_fn(q, bio);
1970 
1971                 bio = bio_list_pop(current->bio_list);
1972         } while (bio); 
```

Enter fullscreen mode Exit fullscreen mode

将向我们展示它从一个块设备(在我们的例子中是一个特定的磁盘)获取请求队列，并发出`make_request_fn`。

这将使我们看到我们的 RAID 由哪些块设备组成:

```
$ mdadm --misc -D /dev/md0 
/dev/md0:
        Version : 1.2
  Creation Time : Mon Nov 30 11:15:51 2015
     Raid Level : raid0
     Array Size : 3989504 (3.80 GiB 4.09 GB)
   Raid Devices : 8
  Total Devices : 8
    Persistence : Superblock is persistent

    Update Time : Mon Nov 30 11:15:51 2015
          State : clean 
 Active Devices : 8
Working Devices : 8
 Failed Devices : 0
  Spare Devices : 0

     Chunk Size : 512K

           Name : alien:0 (local to host alien)
           UUID : d2960b14:bc29a1c5:040efdc6:39daf5cf
         Events : 0

    Number Major Minor RaidDevice State
       0 1 0 0 active sync /dev/ram0
       1 1 1 1 active sync /dev/ram1
       2 1 2 2 active sync /dev/ram2
       3 1 3 3 active sync /dev/ram3
       4 1 4 4 active sync /dev/ram4
       5 1 5 5 active sync /dev/ram5
       6 1 6 6 active sync /dev/ram6
       7 253 0 7 active sync /dev/dm-0 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧——最后一个设备是我们奇怪的`/dev/dm-0`。

同样，我从一开始就知道这一点，并试图找到 SystemTap 问题的根源。但是 SystemTap 只是研究内核代码和进行更深入思考的动力，这很好。这再次证明了调查任何问题的最佳工具，无论是性能问题还是 bug，都是您的大脑和经验。