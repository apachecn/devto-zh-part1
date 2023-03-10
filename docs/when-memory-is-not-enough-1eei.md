# 内存不够用的时候...

> 原文：<https://dev.to/dzeban/when-memory-is-not-enough-1eei>

## [T1】简介](#intro)

[上次](https://dev.to/blog/restrict-memory/)我在想如何限制程序的内存消耗。作为我之前旅程的战利品，我得到了[libmemrestrict](https://github.com/dzeban/restrict-memory/blob/master/memrestrict.c)——一个在分配函数上实现包装器的库，比如`malloc`来计算内存使用——和[ptrace-restrict](https://github.com/dzeban/restrict-memory/blob/master/ptrace-restrict.c)——一个基于 ptrace 的工具，它拦截`brk`、`sbrk`和`mmap`系统调用来做同样的事情。

但是等等，为什么要在内存受限的环境中工作呢？这真的是一个普遍的问题吗？你还记得上一次 OOM killer 拍你的应用是什么时候吗？你在编程的时候总是考虑内存消耗吗？我相信你不会，因为内存是廉价的，如果你的应用程序是饥饿的-只需添加几个内存！

然而，你不能无限地增加越来越多的内存，这并不是因为你没有无限量的内存。今天的软件必须处理像大数据这样的事情，在这种情况下，您无法将输入放入一个数组中。你需要在内存、存储器和网络之间分配数据。你需要算法或者至少是技术来以那种方式处理数据。

所以我在这里，试图用一个流行的问题来解决这类问题，尽管有些琐碎——如何在 2mb 的 RAM 中对一百万个整数(4 MiB 的数据)进行排序？这可以推广到对不适合 RAM 的数据进行排序的问题，这也是我在这里试图解决的问题。

## 议程

我们需要写一个程序，将从一个文件中排序整数。为了生成这种类型的文件，我编写了简单的实用程序 [randints](https://github.com/dzeban/cs/blob/master/number/randints.c) 和 [rangeints](https://github.com/dzeban/cs/blob/master/number/rangeints.c)

程序应该在 stdout 上以文本格式输出排序后的数组。

程序应该测量它的工作时间并输出到 stderr <sup id="fnref:time">[1](#fn:time)</sup> 。

该程序应在内存量至少比文件大小小两倍的情况下工作(例如，4mb 文件使用 2mb 内存)。为了检查这一点，我们将使用`libmemrestrict`或`ptrace-restrict`。

尽管有这么好的限制工具，我们还是不会在一些方法中使用它们。例如，对于`mmap`来说，这是没有意义的——我们需要限制*物理的* RAM 的使用，而不是*虚拟的*(详见相应章节)。

程序将被测试以解决原来的问题(在 2 MiB 中排序 4 MiB)。此外，我将在一个有 128 MiB 内存的虚拟机上运行它，对 500 MB (125 百万个 4 字节)的整数进行排序。

## 天真道

让我们尝试用一种简单的方法对它进行排序，以获得一个基线，并看看我们将使用多少 RAM。[这里的](https://github.com/dzeban/cs/blob/master/sorting/external/naive.c)是一个实现，它简单地将整个文件读入一个整数数组，并对其调用`qsort`(额外的好处是，您可以通过`realloc`找到一个简单的动态数组实现)。

我们将尝试用这个程序对 4MB 的数据进行分类。没有任何限制它工作:

```
$ ./naive 4M.bin > /dev/null
4000000 bytes sorted in 0.323146 seconds 
```

Enter fullscreen mode Exit fullscreen mode

但是没什么意思。当我们试图将它限制在 2 MiB 时，我们会得到一个分段错误。

```
$ LD_PRELOAD=./libmemrestrict.so ./naive ints > ints.sorted 
Segmentation fault 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们将 <sup id="fnref:ms-threshold">[2](#fn:ms-threshold)</sup> 限制增加到 4 MiB 来保存所有数据，我们仍然会失败。

```
$ MR_THRESHOLD=5000000 LD_PRELOAD=./libmemrestrict.so ./naive ints > ints.sorted 
Segmentation fault 
```

Enter fullscreen mode Exit fullscreen mode

显然，有东西试图获得更多的内存，很明显，是`qsort`。让我们看看借助[Valgrind 的 massif](https://dev.to/blog/valgrind/) :
它要多少内存

```
$ valgrind --tool=massif ./naive ints 
$ ms_print massif.out.10676 
```

Enter fullscreen mode Exit fullscreen mode

这里有一张好看的图片:

```
 MB
    8.819^               :::::::::::::::::::::::::::#                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |                   :                          #                         
     |            :::::::@                          #:::::::::::::::::::::::: 
     |            :      @                          #                         
     |            :      @                          #                         
     |            :      @                          #                         
     |            :      @                          #                         
     |            :      @                          #                         
     |      @@@@@@:      @                          #                         
     |      @     :      @                          #                         
     |      @     :      @                          #                         
     |   :::@     :      @                          #                         
     | :::  @     :      @                          #                         
       0 +----------------------------------------------------------------------->Gi
     0                                                                   1.721 
```

Enter fullscreen mode Exit fullscreen mode

您可能会看到一些翻倍的分配，达到 4 MiB——这是我的动态数组，然后再为`qsort`增加 4 MiB。以下是一些统计数据:

```
-------------------------------------------------------------------------------------
  n time(i) total(B) useful-heap(B) extra-heap(B) stacks(B)
-------------------------------------------------------------------------------------
 21 173,222,581 5,247,504 4,000,568 1,246,936 0
 22 173,223,802 5,246,920 4,000,000 1,246,920 0
 23 173,226,655 5,247,504 4,000,568 1,246,936 0
 24 173,229,202 5,246,920 4,000,000 1,246,920 0
 25 173,229,311 9,246,928 8,000,000 1,246,928 0
 26 869,058,772 9,246,928 8,000,000 1,246,928 0
86.52% (8,000,000B) (heap allocation functions) malloc/new/new[], --alloc-fns, etc.
->43.26% (4,000,000B) 0x400A26: readfile (in /home/avd/dev/cs/sorting/external/naive)
| ->43.26% (4,000,000B) 0x400ACD: main (in /home/avd/dev/cs/sorting/external/naive)
|   
->43.26% (4,000,000B) 0x35D40383F7: qsort_r (in /usr/lib64/libc-2.18.so)
| ->43.26% (4,000,000B) 0x400B3D: main (in /home/avd/dev/cs/sorting/external/naive)
|   
->00.00% (0B) in 1+ places, all below ms_print's threshold (01.00%) 
```

Enter fullscreen mode Exit fullscreen mode

我使用了 400 万(有用的)字节，另外 400 万字节由`qsort_r`使用。massif 还使用了 1.2 MB 的额外堆。

看起来在这种情况下,`qsort`在空间复杂度上表现为 O(n)。<sup id="fnref:qsort">[3](#fn:qsort)T4】</sup>

好的，它能在 128 MiB 的内存中对 500 MB 进行排序吗？

```
$ ./naive 500M.bin > /dev/null
Segmentation fault 
```

Enter fullscreen mode Exit fullscreen mode

当然不是。至于性能:

```
$ ./naive 4M.bin > /dev/null
4000000 bytes sorted in 0.322712 seconds 
```

Enter fullscreen mode Exit fullscreen mode

因此，天真排序在不受限制时工作良好，在受限制时会失败，并且`qsort`由于某种奇怪的原因需要 O(n)内存 <sup id="fnref:massif-qsort">[4](#fn:massif-qsort)</sup> 。

## 映射文件

使用`mmap`是在少量 RAM 中对大量数据进行排序的一种既好又简单的方法。通过使用`mmap`,您将在 RAM 和交换空间之间平衡数据的负担转移到了操作系统内核。

它是这样工作的:

1.  你把整个文件和数据一起存入内存。
2.  从`mmap`你得到指向你的数据的指针。
3.  您在`mmap` ed 指针下调用数据排序算法。

就是这样！从现在起，你将不会超过物理内存的限制，即使你是排序文件比你的内存大得多。为了理解它为什么工作，你需要一些关于操作系统中内存管理的知识。

一个进程所代表的每一个程序都有自己私有的、与其他进程隔离的**虚拟**地址空间。地址空间的长度受到 CPU 地址总线宽度的限制，例如，对于 32 位 CPU，它是 2 <sup>32</sup> 即 4 GiB。

进程中通过`malloc`、`new`或其他方式发生的所有分配都是一个**虚拟**内存分配。内核内存管理子系统将分配的虚拟内存映射到物理内存。通常，这是在懒惰模式下完成的。这意味着无论何时进程请求一定数量的内存，内核都会立即满足这个请求，但是**不会**进行实际分配——在这种情况下，我们说虚拟内存页面是**而不是映射**(到物理页面框架)。每当这种未映射的页面被访问(例如，它被写入)时， [MMU](http://en.wikipedia.org/wiki/Memory_management_unit) 将生成“页面错误”异常，内核将通过将虚拟内存页面映射到物理页面帧来处理该异常。从现在开始，一个页面被映射到页面中的虚拟地址，MMU 会将该页面中的虚拟地址转换为 RAM 中的物理地址。

另一方面，如果您记得虚拟地址空间只受 CPU 地址总线大小的限制，您可能会遇到这样的情况:程序可以分配比 ram 中可用的内存更多的内存。例如，您的 32 位系统只有 256 MiB 的 RAM，但是进程可以分配和使用 1gb 的内存。在这种情况下，内存页面不能保存在 RAM 中，而是将被**交换**，即从 RAM 中驱逐到硬盘驱动器等后备存储中。每当进程请求交换的页面时，内核将从磁盘获取它并加载到 RAM 中(可能通过替换其他页面)。

正如你所看到的，内核可以很好地在内存和磁盘之间分配数据，所以在我们的任务中利用这个工具是很自然的。当我们对文件执行`mmap`操作时，内核将为我们的文件保留一个不会被映射的虚拟地址范围。每当我们试图通过改变字节、移动数组支点或其他方式来访问它们时，内核就会从磁盘上的输入文件中获取数据并存入 RAM。每当我们耗尽**物理**内存时，内核就会驱逐一些页面来交换空间。这样，我们将能够在磁盘上的原始文件、RAM 和交换空间之间平衡我们的数据。

该方法中唯一的限制是虚拟地址空间，这并不是很大的限制(对于 32 位系统是 4 GiB，对于 64 位系统是 256 TiB <sup id="fnref:64-bit-space">[5](#fn:64-bit-space)</sup> )，并且交换空间可能很大，因为现在硬盘驱动器很便宜。

还要注意，因为`mmap`将整个文件加载到**虚拟**内存中，所以我们不能使用`libmemrestrict`或`ptrace-restrict`，因为它们占用了虚拟内存本身。如果我们试图限制在 10M 虚拟内存中排序 100M 数据，`mmap`程序将失败:

```
$ qemu-x86_64 -R 10M ./mmaped 100M.bin
mmap stack: Cannot allocate memory 
```

Enter fullscreen mode Exit fullscreen mode

这并不奇怪，因为`mmap`将整个文件加载到**虚拟内存**中，然后内核在实际物理内存和交换之间分配它。因此，我们需要至少 100M 的虚拟内存(加上一些额外的空间给`qemu`本身)来将文件映射到内存中。

这就是为什么对于这种排序方法，我将使用一个具有 128 MiB 内存的虚拟机。这里是[我的 mmap 排序程序](https://github.com/dzeban/cs/blob/master/sorting/external/mmaped.c)。你知道吗？它就像一个魔咒！

```
$ free -m
               total used free shared buffers cached
Mem: 119 42 76 0 4 16
-/+ buffers/cache: 21 97
Swap: 382 0 382

$ ll -h 500M.bin
-rw-r--r-- 1 root root 477M Feb 3 05:39 500M.bin

$ ./mmaped 500M.bin > /dev/null
500000000 bytes sorted in 32.250000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

以下是`top`信息:

```
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
3167 root 20 0 480m 90m 90m R 84.6 76.4 1:18.65 mmaped 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们使用了 500 MB 的虚拟内存**<sup id="fnref:MiB">[6](#fn:MiB)</sup>，而实际常驻内存只有 90 MiB。**

 **如果我们在对 500 MB 进行排序时查看更详细的`vmstat`输出，我们将看到内核是如何在交换、磁盘缓存、缓冲区和空闲内存之间进行平衡的:

```
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
 0  0      0  77776   2120  15104    1   27   710   971   24   34  3  1 95  1
 1  1      0   2060    488  90068    1   27   785  1057   25   37  3  1 95  1
 1  0    928   3400     60  89744    1   27   799  1092   25   38  3  1 94  1
 0  2   1908   1928    212  92040    1   27   852  1174   26   40  4  1 94  1
 0  2   3436   2360    280  93056    1   27   911  1282   28   42  4  1 94  2
 0  0   5272   3688    196  94688    1   27  1066  1471   31   48  4  1 93  2
 0  0   5272   3720    204  94700    1   27  1064  1469   31   48  4  1 93  2 
```

Enter fullscreen mode Exit fullscreen mode

开始时，我们有大约 70 MiB 的空闲内存、空的交换空间和一些分配给 I/O 缓冲区和磁盘缓存的内存。然后，我们执行了一个`mmap`，所有的内存都被转移到了磁盘缓存中。当空闲内存耗尽时，内核开始使用交换空间，我们可以看到它随着 I/O 负载的增加而增加。我们最终会遇到几乎所有内存都专用于磁盘缓存的情况，尽管这没什么，因为当应用程序需要内存时，磁盘缓存页面是第一个被窃取的对象。

因此，借助于`mmap`进行排序是一个简单的技巧，只需要简单地理解内存管理，但它为您提供了一个在少量 RAM 中处理大量数据的快速而简单的解决方案。

## 通用外部排序

好吧，假设你不能使用`mmap`，比如你想在 32 位系统上对 5 个 GiB 文件进行排序。你会怎么做？

有一个众所周知且流行的方法来完成这个叫做*外部合并排序*的任务。动机很简单-如果你没有足够的内存来保存你的数据，你只需要使用一些外部存储，如硬盘。当然，您必须一点一点地处理数据，因为您仍然只有少量的主内存。

外部合并排序的工作方式如下:

1.  您将数据分成可用内存缓冲区大小的块。
2.  每个块在内存缓冲区中排序，并写入外部存储。
3.  现在你的存储器上有`filesize/buffersize`块了。
4.  最后，从每个块中读取`buffersize/#chunks`片段，将它们合并到缓冲区中，并输出到结果文件。

我做了一些[琐碎的，绝对没有优化的实现](https://github.com/dzeban/cs/blob/master/sorting/external/external-merge.c)简单的工作:

```
$ LD_PRELOAD=./libmemrestrict.so ./external-merge 4M.bin 1048576 > /dev/null
4194304 bytes sorted in 0.383171 seconds 
```

Enter fullscreen mode Exit fullscreen mode

我们已经使用 1 MiB 缓冲区在 2 MiB 内存中排序。

现在让我们排序 500MB。首先，禁用交换——我们手动处理块交换:

```
$ swapoff /dev/vda5 
```

Enter fullscreen mode Exit fullscreen mode

丢弃缓存:

```
$ echo 3 > /proc/sys/vm/drop_caches 
```

Enter fullscreen mode Exit fullscreen mode

检查空闲内存:

```
$ free -m
             total used free shared buffers cached
Mem: 119 28 90 0 0 6
-/+ buffers/cache: 21 97
Swap: 0 0 0 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 50M 作为缓冲区——比文件大小小 10 倍。

```
$ ./external-merge 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 120.115180 seconds 
```

Enter fullscreen mode Exit fullscreen mode

天哪，两分钟！这是为什么呢？这里，性能的主要杀手是 I/O。有三种情况会导致大量的 I/O 并降低速度。在分割阶段，我们通过一个小的缓冲区顺序读取文件。在合并阶段，我们不断地打开和关闭块文件。最后但并非最不重要的是输出——在合并阶段，我们将整个缓冲区(50 MB，1250 万个整数)输出到 stdout，尽管它被重定向到`/dev/null`，但产生了沉重的负载。我们可以关掉它。总而言之，在`mmaped`中，我们在程序结束时一次性输出所有内容，并且不在性能计数器中计算。因此，如果我们关闭输出**，我们将在大约 90 秒内运行——快 25%**。

```
$ ./external-merge-no-output 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 87.140000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

至于内存消耗——没问题。如果我们尝试在 *massif* 下运行它，我们会看到峰值消耗是我们的缓冲区大小加上一些额外的堆。

```
-------------------------------------------------------------------------------------
Command:            ./external-merge 500M.bin 50000000
Massif arguments:   (none)
ms_print arguments: massif.out.17423
-------------------------------------------------------------------------------------

    MB
47.75^                                                                  ::::: 
     |#::::::@:::::::::::@:::::::::@:::@::::@::::@::::::::@::::@::::@:::@     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
     |# : :  @ :  : :  : @  : :    @   @    @    @   :    @    @    @   @     
   0 +----------------------------------------------------------------------->Gi
     0                                                                   332.5

Number of snapshots: 98
 Detailed snapshots: [4 (peak), 10, 20, 29, 32, 35, 38, 45, 48, 54, 64, 74, 84, 94]

-------------------------------------------------------------------------------------
  n        time(i)         total(B)   useful-heap(B) extra-heap(B)    stacks(B)
-------------------------------------------------------------------------------------
  0              0                0                0             0            0
  1        119,690              584              568            16            0
  2        123,141       50,004,496       50,000,568         3,928            0
  3      4,814,014       50,005,080       50,001,136         3,944            0
  4      4,817,234       50,005,080       50,001,136         3,944            0
99.99% (50,001,136B) (heap allocation functions) malloc/new/new[], --alloc-fns, etc.
->99.99% (50,000,000B) 0x400FA2: external_merge_sort (in /root/external-merge)
| ->99.99% (50,000,000B) 0x40128E: main (in /root/external-merge)
|   
->00.00% (1,136B) in 1+ places, all below ms_print's threshold (01.00%) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将内存限制为 50 MB <sup id="fnref:extra-restrict">[7](#fn:extra-restrict)</sup> 我们会看到它是有效的:

```
$ LD_PRELOAD=./libmemrestrict.so MR_THRESHOLD=51000000 ./external-merge 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 87.900000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

好吧，内存消耗没问题，性能没那么好。回想一下`mmaped`用了 32 秒就做到了这一点。让我们看看如何提高我们的 90 秒。

让我们用 gprof 来分析这个不错的程序。构建插入指令的二进制文件

```
$ gcc -pg -g -Wall -Wextra external-merge.c -o external-merge-gprof 
```

Enter fullscreen mode Exit fullscreen mode

并多次调用这个程序来累积统计数据。为此，我们将使用我在 gprof 上的文章[中的脚本。下面是输出:](https://dev.to/blog/gprof-gcov/) 

```
%   cumulative   self              self     total           
time   seconds   seconds    calls  Ts/call  Ts/call  name    
81.98    432.87   432.87                             compar
18.17    528.82    95.95                             print_arr
0.00    528.82     0.00     1100     0.00     0.00  form_filename
0.00    528.82     0.00      100     0.00     0.00  merge
0.00    528.82     0.00      100     0.00     0.00  save_buf
0.00    528.82     0.00       10     0.00     0.00  external_merge_sort
0.00    528.82     0.00       10     0.00     0.00  split 
```

Enter fullscreen mode Exit fullscreen mode

我们大部分时间都花在了整理和打印上。但是也不要忘记 gprof 不会显示花费在系统调用和 I/O 上的时间。

我认为这里有两点需要改进:

*   用多线程和 I/O 技巧调优外部排序
*   考虑一种不同的排序算法

因此，通用外部合并排序是一个非常简单的想法，可以对小 RAM 中的大量数据进行排序，但通常不进行调整和改进，它的速度很慢。

## 调谐外部排序

我们可以尝试的事情之一是在外部排序的拆分和合并阶段使用多线程。然而，在这种情况下，这并不是一个好主意。

在分离阶段使用多线程没有意义，因为只有一个缓冲区可以容纳数据。但是我们可以尝试建议内核如何读取数据。为此有两个功能:

1.  虽然这是 Linux 特有的。
2.  `posix_fadvise`同`POSIX_FADV_SEQUENTIAL`。

这些函数将通知内存管理子系统我们将如何读取数据。在我们的例子中，我们按顺序读取它，因此在页面缓存中看到文件内容会很好。

在合并阶段，我们可以通过为每个块创建一个专用线程来避免块文件的常量`open` / `close`。每个线程将保持其文件打开，并将在线程偏移量处填充缓冲区。当缓冲器充满时，它将被排序 <sup id="fnref:nway-merge">[8](#fn:nway-merge)</sup> 并输出。此外，我们将为每个线程使用`readahead`。

[这里的](https://github.com/dzeban/cs/blob/master/sorting/external/mt-external-merge.c)是调优和多线程版本的外部合并排序。

好吧，正如我所说，多线程在这里不是一个好主意。所有这些线程的东西都很好，但是对于单核 CPU 来说，它没有显示出任何效果:

```
$ ./mt-ext-merge 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 117.380000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

是带印刷的版本。这里是没有打印的版本:

```
$ ./mt-ext-merge-no-output 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 91.040000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

你可能认为这是因为我们很难在单个 CPU 上调度几十个线程。好了，我们在四核机上(英特尔酷睿 i7-3612QM CPU @ 2.10GHz)和其他方法对比一下:

```
$ ./naive 500M.bin > /dev/null 
500000000 bytes sorted in 23.040499 seconds

$ ./mmaped 500M.bin > /dev/null
500000000 bytes sorted in 23.542076 seconds

$ ./external-merge 500M.bin 50000000 > /dev/null 
500000000 bytes sorted in 39.228695 seconds

$ ./mt-external-merge 500M.bin 50000000 > /dev/null 
500000000 bytes sorted in 41.062793 seconds

$ ./external-merge-no-output 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 28.893745 seconds

$ ./mt-external-merge-no-output 500M.bin 50000000 > /dev/null
500000000 bytes sorted in 28.368976 seconds 
```

Enter fullscreen mode Exit fullscreen mode

现在有了 100 个块(100 个线程):

```
$ ./external-merge-no-output 500M.bin 5000000 > /dev/null
500000000 bytes sorted in 27.107728 seconds

$ ./mt-external-merge-no-output 500M.bin 5000000 > /dev/null
500000000 bytes sorted in 28.558468 seconds 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到`external-merge`和`mt-external-merge`之间的性能没有变化。这是为什么呢？因为**在大多数情况下，多线程并不是 I/O 绑定应用的解决方案**。尽管如此，有些情况下生成一些线程会加快速度:

*   您的执行线程是独立的
*   您的 I/O 资源可以并行工作，例如 RAID

这里的例子有一些在独立区域渲染图像的图形应用程序，或者为多个结果进行大量计算的科学应用程序。

在多线程外部排序中，线程是相互依赖的——你必须等待主线程对缓冲区进行排序，然后才能从块中进行进一步的读取。此外，读取一个片要比排序整个缓冲区快得多，所以大多数时候线程都在等待主线程完成。

这就是为什么多线程不会帮助我们，所以我们需要寻找其他方法来改善。

## 特殊排序算法

现在，假设我们知道数据的分布，让我们尝试使用某种比快速排序更好的排序算法。就像我们知道我们在对整数进行排序一样，为什么不玩这个呢？有一些排序算法是专门为特殊类型的数据设计的，这些数据可以分为两类:

1.  不要使用比较。
2.  甚至不需要在内存中加载输入数组。

众所周知，这种算法比 O(n log(n))更有效，O(n log(n))是快速排序等基于比较的排序的下限。当然，不是每个算法都适合我们，因为我们有内存限制。例如，基数排序和其他种类的桶排序对我们没有帮助，因为它需要额外的桶内存，尽管有[就地基数排序](http://www.drdobbs.com/architecture-and-design/algorithm-improvement-through-performanc/220300654)的实现。无论如何，这样的实现需要对每个基数多次读取整个数据集——二进制基数排序需要 32 次，这太多了。此外，由 MSD 基数排序产生深度递归本身也是一种内存消耗。这就是为什么我想到使用计数排序。

## 计数排序

如果我们知道我们的数据很大，但它的范围很小，那么我们可以使用计数排序。这个想法非常简单——我们将保存一个计数器数组，而不是在内存中保存数据。我们将顺序读取数据，然后递增相关的计数器。最重要的是，计数排序的时间复杂度是线性的，空间复杂度与通常很小的范围成比例。

一个简单的实现可以处理从 0 到某个`N`的连续整数范围。有一个数组大小的范围，其中整数对应于数组索引。这是我在 github 上实现的[，没有任何调优](https://github.com/dzeban/cs/blob/master/sorting/counting-array.c)[9](#fn:buffering) :
表现非常好

```
$ ./counting-array 500M-range.bin 1000000 > /dev/null
Range is 1000000
500000000 bytes sorted in 3.240000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

是的，在单 CPU 的 128 MiB 机器上，半千兆字节的数据在 3.5 秒内被分类。与`mmap` :
上的`qsort`进行比较

```
$ ./mmaped 500M-range.bin > /dev/null
500000000 bytes sorted in 76.150000 seconds 
```

Enter fullscreen mode Exit fullscreen mode

快了 23 倍！

但是无论如何，你应该意识到计数排序所隐含的限制:只有来自小的和连续的范围的整数(或等价的)。我还尝试用散列表和二分搜索法树开发非连续范围的计数排序- [这里是代码](https://github.com/dzeban/cs/blob/master/sorting/counting.c)。然而，它的性能相当糟糕，不幸的是，我仍然无法解释这一点。

无论如何，我们可以更进一步，假设在我们的范围内的数字是唯一的。那么，值的计数器可能只有两种状态——存在或不存在，所以我们可以用一个位来表示计数器。这将极大地压缩我们的数组，尽管在这种情况下，我们甚至不需要数组。我们可以将每个数字存储为一位，从而将数据转换为一个位向量——在读取文件时，如果文件中有整数 N，则设置第 N 位。位向量形成后，读取并写入对应于所设置位的输出文件编号。

位向量解决方案需要更多的关注，因为尽管它看起来很紧凑，但您可能会违反您的限制，例如，要对整数范围(2 <sup>32</sup> )内的数字数组进行排序，您需要为每个整数设置 1 位，即 4294967296 位= 536870912 字节= 512 MiB。以我的情况来说，我只有 128 MiB，所以它对我不起作用。但是有些情况下你*会*像老板一样受益——读读乔恩·本特利的《编程珍珠》中[的一个精彩故事。](http://www.cs.bell-labs.com/cm/cs/pearls/cto.html)

这里的寓意是“知道你的数据是极其有用的”。

## 重述

在写这篇文章的最后 5 个月里，我做了很多事情——我已经实现了十几个程序，提出了一些好的想法，失败了更多的想法，但我仍然有事情要尝试和/或修复。现在是下结论的时候了。

在小内存中对数据进行排序的简单问题揭示了一整类我们通常不会想到的特性:

*   常见的广泛使用的算法并不适用于任何问题。
*   动态调试和概要分析非常有用，并且具有示范性。
*   除非你完全依赖内核，否则 I/O 就是个婊子。
*   多线程并不是提高性能的灵丹妙药。
*   了解您的数据和环境。

至于排序这里是一个结果表:

```
|Test case |Naive     | mmap and  | External   | Multithreaded       | Counting |
|          |QuickSort | QuickSort | merge sort | external merge sort | sort     |
|----------|:--------:|-----------|------------|---------------------|----------|
|4 MiB in  | Segfault |    N/A    |   0.38s    |     0.41s           |   0.01   |
|2 MiB     |          |           |            |                     |          |
|----------|----------|-----------|------------|---------------------|----------|
|500 MB in | Segfault |   32.25s  |   87.14s   |     91.04           |   3.24   |
|128 MiB   |          |           |            |                     |          | 
```

Enter fullscreen mode Exit fullscreen mode

底线是**“了解你的数据，并为它开发一个简单的算法”**。

感谢您的关注。

## 参考文献

*   [使用 Python](http://neopythonic.blogspot.ru/2008/10/sorting-million-32-bit-integers-in-2mb.html) 对 2MB 内存中的 100 万个 32 位整数进行排序——使用生成器、`array`模块和`heapq`以 Python 方式进行简单的外部排序。此外，对缓冲的优势也有很好的解释。

*   [大型数据集的外部排序](http://www.umbrant.com/blog/2011/external_sorting.html) -由王晓康执行的具有性能测量的外部排序。

*   有效地对大于内存的数据集进行排序——Reddit 上关于王晓康文章的帖子不错，展示了一些不同的方法及其优缺点。

*   [剥牡蛎(编程珍珠的专栏 1)](http://www.cs.bell-labs.com/cm/cs/pearls/cto.html)-关于做大排序的令人惊讶的书面故事。详细讨论了位向量的概念。

*   [多线程文件 I/O](http://www.drdobbs.com/parallel/multithreaded-file-io/220300055) -多线程 I/O 在单个磁盘和 RAID5 阵列上的表现。

*   [通过性能测量改进算法:第 2 部分](http://www.drdobbs.com/architecture-and-design/algorithm-improvement-through-performanc/220300654)介绍就地基数排序实现。

*   [并行排序算法](http://stackoverflow.com/questions/3969813/which-parallel-sorting-algorithm-has-the-best-average-case-performance) -关于多节点计算机系统排序的综合资源列表。

* * *

1.  我们不能简单地在`time`实用程序下启动程序，因为它包括将文件读入内存的时间和输出到控制台的时间。<sup>【归来】</sup>
2.  libmemrestrict 从环境中获取配置。库的常见做法，例如`LD_PRELOAD`和`LD_LIBRARY_PATH`是众所周知的 ld-linux.so 的环境变量，还有一个不太为人所知但非常有用的`LD_DEBUG`环境变量用于[联动调试](http://www.bnikolic.co.uk/blog/linux-ld-debug.html)。<sup>【返回】</sup>
3.  这很奇怪，因为 qsort 已经就位，并且使用了罗伯特·塞奇威克建议的优化方法来保证 O(log n)的空间复杂度。您可以深入研究 glibc qsort 的实现。 <sup>【归来】</sup>
4.  不过有一种奇怪的行为。例如，如果我将内存限制为 5.3 MB，它将工作，而*而不是*需要 O(n)内存。我还在调查这件事。<sup>【归来】</sup>
5.  [http://en . Wikipedia . org/wiki/X86-64 #虚拟地址空间详情](http://en.wikipedia.org/wiki/X86-64#Virtual_address_space_details) <sup>【返回】</sup>
6.  注意 **MiB** 是 2 <sup>20</sup> 和 **MB** 是 10<sup>6</sup>= 100 万。因此，500 MB = 500 000 000 字节，也就是 500 000 000 > > 20 = 476 MiB。<sup>【返回】</sup>
7.  外加 500 KB 用于存放块路径的临时字符串。<sup>【返回】</sup>
8.  在这种情况下，这是一种 N 路合并。<sup>【返回】</sup>
9.  第二个参数是以元素为单位的缓冲区大小。缓冲大大提高了性能，因为它不会从文件中读取 4 个字节。<sup>【返回】</sup>**