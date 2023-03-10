# Valgrind

> 原文：<https://dev.to/dzeban/valgrind-48d6>

与普遍的看法相反， *Valgrind* 不是一个单一的工具，而是一套这样的工具，其中 *Memcheck* 是默认工具。在撰写本文时，Valgrind 套件包括:

*   内存检查-内存管理错误检测。
*   cache grind——CPU 缓存访问分析工具。
*   massif–采样堆分析器。
*   竞争条件检测器。
*   DRD——检测多线程应用程序错误的工具。

另外还有一些非官方的工具不在 *Valgrind* 中，而是作为[补丁](http://valgrind.org/downloads/variants.html)发布。

Valgrind 最大的优点是我们不需要以任何方式重新编译或修改我们的程序，因为 Valgrind 工具使用仿真作为一种分析方法。所有这些工具都使用模拟应用程序运行时的公共基础设施——内存管理功能、CPU 缓存、线程原语等。那是我们的程序执行和被 Valgrind 分析的地方。

在下面的例子中，我将使用我的 [block_hasher](https://github.com/dzeban/block_hasher) 程序来说明 profilers 的用法。因为它是一个小而简单的工具。

现在我们来看看 *Valgrind* 能做什么。

## Memcheck

好的，那么 *Memcheck* 是一个内存错误检测器——它是程序员工具箱中最有用的工具之一。

让我们在*内存检查*
下启动我们的哈希函数

```
$ valgrind --leak-check=full ./block_hasher -d /dev/md126 -b 1048576 -t 10 -n 1000
==4323== Memcheck, a memory error detector
==4323== Copyright (C) 2002-2010, and GNU GPL'd, by Julian Seward et al.
==4323== Using Valgrind-3.6.0 and LibVEX; rerun with -h for copyright info
==4323== Command: ./block_hasher -d /dev/md126 -b 1048576 -t 10 -n 1000
==4323== 
==4323== 
==4323== HEAP SUMMARY:
==4323== in use at exit: 16 bytes in 1 blocks
==4323== total heap usage: 43 allocs, 42 frees, 10,491,624 bytes allocated
==4323== 
==4323== LEAK SUMMARY:
==4323== definitely lost: 0 bytes in 0 blocks
==4323== indirectly lost: 0 bytes in 0 blocks
==4323== possibly lost: 0 bytes in 0 blocks
==4323== still reachable: 16 bytes in 1 blocks
==4323== suppressed: 0 bytes in 0 blocks
==4323== Reachable blocks (those to which a pointer was found) are not shown.
==4323== To see them, rerun with: --leak-check=full --show-reachable=yes
==4323== 
==4323== For counts of detected and suppressed errors, rerun with: -v
==4323== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 6 from 6) 
```

Enter fullscreen mode Exit fullscreen mode

我就不解释什么是*肯定丢失*、*间接丢失*等了——这就是[文档](http://valgrind.org/docs/manual/mc-manual.html)的作用。

从 *Memcheck* 配置文件中，我们可以说除了少量泄漏外没有任何错误，1 块是*仍可到达*。来自
的消息

```
total heap usage: 43 allocs, 42 frees, 10,491,624 bytes allocated 
```

Enter fullscreen mode Exit fullscreen mode

我忘了给某个地方打电话。这是真的，在`bdev_open`中我为`block_device`分配了结构，但是在`bdev_close`中它没有释放。对了，有意思的是 *Memcheck* 报告大约丢失 16 字节，而`block_device`是应该占用`4 + 8 = 12`字节的`int`和`off_t`。哪里还有 4 个字节？结构是 8 字节对齐的(对于 64 位系统)，这就是为什么`int`字段用 4 字节填充。

总之，我已经[修复了](https://github.com/dzeban/block_hasher/commit/f86fa71c45c3a59ced99b74b44a30cb8d94ba72d)内存泄漏:

```
@@ -240,6 +241,9 @@ void bdev_close( struct block_device *dev )
         perror("close");
     }

+    free(dev);
+    dev = NULL;
+
     return;
 } 
```

Enter fullscreen mode Exit fullscreen mode

检查:

```
$ valgrind --leak-check=full ./block_hasher -d /dev/md126 -b 1048576 -t 10 -n 1000
==15178== Memcheck, a memory error detector
==15178== Copyright (C) 2002-2010, and GNU GPL'd, by Julian Seward et al.
==15178== Using Valgrind-3.6.0 and LibVEX; rerun with -h for copyright info
==15178== Command: ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 1000
==15178== 
==15178== 
==15178== HEAP SUMMARY:
==15178== in use at exit: 0 bytes in 0 blocks
==15178== total heap usage: 43 allocs, 43 frees, 10,491,624 bytes allocated
==15178== 
==15178== All heap blocks were freed -- no leaks are possible
==15178== 
==15178== For counts of detected and suppressed errors, rerun with: -v
==15178== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 6 from 6) 
```

Enter fullscreen mode Exit fullscreen mode

很高兴看到。

作为一份简历，我想说 *Memcheck* 能做的事情很多。不仅在检测记忆错误，而且在解释。说“嘿，你这里有一些错误”是轻描淡写的–要修复错误，最好知道原因是什么。而 *Memcheck* 做到了。太好了，甚至被列为系统程序员岗位的技能。

待办事项:

*   更多内存错误示例
*   追踪原点

## CacheGrind

*cache grind*–CPU 缓存访问分析器。让我惊讶的是它是如何跟踪缓存访问的——*cache grind*模拟它，参见文档摘录:

> 它对你的 CPU 中的 I1、D1 和 L2 高速缓存进行了详细的模拟，因此可以准确地指出代码中高速缓存未命中的来源。

如果你认为这很容易，请花 90 分钟阅读[这篇伟大的文章](http://www.lighterra.com/papers/modernmicroprocessors/)。

让我们收集个人资料！

```
$ valgrind --tool=cachegrind ./block_hasher -d /dev/md126 -b 1048576 -t 10 -n 1000
==9408== Cachegrind, a cache and branch-prediction profiler
==9408== Copyright (C) 2002-2010, and GNU GPL'd, by Nicholas Nethercote et al.
==9408== Using Valgrind-3.6.0 and LibVEX; rerun with -h for copyright info
==9408== Command: ./block_hasher -d /dev/md126 -b 1048576 -t 10 -n 1000
==9408== 
--9408-- warning: Unknown Intel cache config value (0xff), ignoring
--9408-- warning: L2 cache not installed, ignore LL results.
==9408== 
==9408== I refs: 167,774,548,454
==9408== I1 misses: 1,482
==9408== LLi misses: 1,479
==9408== I1 miss rate: 0.00%
==9408== LLi miss rate: 0.00%
==9408== 
==9408== D refs: 19,989,520,856 (15,893,212,838 rd + 4,096,308,018 wr)
==9408== D1 misses: 163,354,097 ( 163,350,059 rd + 4,038 wr)
==9408== LLd misses: 74,749,207 ( 74,745,179 rd + 4,028 wr)
==9408== D1 miss rate: 0.8% ( 1.0% + 0.0% )
==9408== LLd miss rate: 0.3% ( 0.4% + 0.0% )
==9408== 
==9408== LL refs: 163,355,579 ( 163,351,541 rd + 4,038 wr)
==9408== LL misses: 74,750,686 ( 74,746,658 rd + 4,028 wr)
==9408== LL miss rate: 0.0% ( 0.0% + 0.0% ) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我会查看缓存未命中。但这里不到 1%，所以这不是问题。

如果你问自己如何使用 Cachegrind，我会告诉你一个工作故事。为了加速一些 RAID 计算算法，我的同事减少了乘法运算，代价是增加了加法运算和复杂的数据结构。理论上，它应该更好地工作，就像在 Karatsuba 乘法中一样。但实际上，情况变得更糟了。经过几天的艰苦调试，我们在 *Cachegrind* 下启动了它，看到缓存缺失率约为 80%。更多的添加调用了更多的内存访问并减少了局部性。所以我们放弃了这个想法。

自从 *perf* 的出现，IMHO cachegrind 不再那么有用，它使用 CPU 的 PMU(性能监控单元)进行实际的缓存分析，因此 perf 更加精确，开销也低得多。

## 质量

*Massif*–heap profiler，从某种意义上来说，它显示了堆分配的动态，即在某个时刻您的应用程序使用了多少内存。

为此， *Massif* 对堆状态进行采样，生成一个文件，该文件稍后在`ms_print`工具的帮助下转换为报告。

好，启动它

```
$ valgrind --tool=massif ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 100
==29856== Massif, a heap profiler
==29856== Copyright (C) 2003-2010, and GNU GPL'd, by Nicholas Nethercote
==29856== Using Valgrind-3.6.0 and LibVEX; rerun with -h for copyright info
==29856== Command: ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 100
==29856== 
==29856== 
```

Enter fullscreen mode Exit fullscreen mode

拿到了一个 *massif.out.29856* 文件。将其转换为文本简介:

```
$ ms_print massif.out.29856 > massif.profile 
```

Enter fullscreen mode Exit fullscreen mode

配置文件包含堆分配直方图

```
 MB
10.01^::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::#
     |:                                                                 #
     |@                                                                 #::
     |@                                                                 # :
     |@                                                                 # ::
     |@                                                                 # ::
     |@                                                                 # ::@
     |@                                                                 # ::@
     |@                                                                 # ::@
     |@                                                                 # ::@
     |@                                                                 # ::@
     |@                                                                 # ::@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
     |@                                                                 # ::@@
   0 +----------------------------------------------------------------------->Gi
     0                                                                   15.63 
```

Enter fullscreen mode Exit fullscreen mode

和一份最值得注意的拨款汇总表。

示例:

```
-------------------------------------------------------------------------------------
  n time(i) total(B) useful-heap(B) extra-heap(B) stacks(B)
-------------------------------------------------------------------------------------
 40 344,706 9,443,296 9,442,896 400 0
 41 346,448 10,491,880 10,491,472 408 0
 42 346,527 10,491,936 10,491,520 416 0
 43 346,723 10,492,056 10,491,624 432 0
 44 15,509,791,074 10,492,056 10,491,624 432 0
100.00% (10,491,624B) (heap allocation functions) malloc/new/new[], --alloc-fns, etc.
->99.94% (10,485,760B) 0x401169: thread_func (block_hasher.c:142)
| ->99.94% (10,485,760B) 0x54189CF: start_thread (in /lib64/libpthread-2.12.so)
| ->09.99% (1,048,576B) 0x6BDC6FE: ???
| |
| ->09.99% (1,048,576B) 0x7FDE6FE: ???
| |
| ->09.99% (1,048,576B) 0x75DD6FE: ???
| |
| ->09.99% (1,048,576B) 0x93E06FE: ???
| |
| ->09.99% (1,048,576B) 0x89DF6FE: ???
| |
| ->09.99% (1,048,576B) 0xA1E16FE: ???
| |
| ->09.99% (1,048,576B) 0xABE26FE: ???
| |
| ->09.99% (1,048,576B) 0xB9E36FE: ???
| |
| ->09.99% (1,048,576B) 0xC3E46FE: ???
| |
| ->09.99% (1,048,576B) 0xCDE56FE: ???
|
->00.06% (5,864B) in 1+ places, all below ms_print's threshold (01.00%) 
```

Enter fullscreen mode Exit fullscreen mode

在上表中，我们可以看到，我们通常分配 10 个 MiB 部分，实际上是 10 个 1 MiB 的数据块(我们的数据块大小)。没什么特别的，但是很有趣。

当然， *Massif* 是有用的，因为它可以显示分配的历史，所有对齐分配了多少内存，以及分配最多的代码段。可惜我没有堆错误。

## Helgrind

*Helgrind* 不是一个剖析器，而是一个检测线程错误的工具。这是一个线程调试器。

我只是展示了我是如何在 Helgrind 的帮助下修复代码中的 bug 的。

当我在它下面启动我的`block_hasher`时，我确信我会有 0 个错误，但是在调试中坚持了几天。

```
$ valgrind --tool=helgrind ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 100
==3930== Helgrind, a thread error detector
==3930== Copyright (C) 2007-2010, and GNU GPL'd, by OpenWorks LLP et al.
==3930== Using Valgrind-3.6.0 and LibVEX; rerun with -h for copyright info
==3930== Command: ./block_hasher -d /dev/md0 -b 1048576 -t 10 -n 100
==3930== 
==3930== Thread #3 was created
==3930== at 0x571DB2E: clone (in /lib64/libc-2.12.so)
==3930== by 0x541E8BF: do_clone.clone.0 (in /lib64/libpthread-2.12.so)
==3930== by 0x541EDA1: pthread_create@@GLIBC_2.2.5 (in /lib64/libpthread-2.12.so)
==3930== by 0x4C2CE76: pthread_create_WRK (hg_intercepts.c:257)
==3930== by 0x4019F0: main (block_hasher.c:350)
==3930== 
==3930== Thread #2 was created
==3930== at 0x571DB2E: clone (in /lib64/libc-2.12.so)
==3930== by 0x541E8BF: do_clone.clone.0 (in /lib64/libpthread-2.12.so)
==3930== by 0x541EDA1: pthread_create@@GLIBC_2.2.5 (in /lib64/libpthread-2.12.so)
==3930== by 0x4C2CE76: pthread_create_WRK (hg_intercepts.c:257)
==3930== by 0x4019F0: main (block_hasher.c:350)
==3930== 
==3930== Possible data race during write of size 4 at 0x5200380 by thread #3
==3930== at 0x4E98AF8: CRYPTO_malloc (in /usr/lib64/libcrypto.so.1.0.1e)
==3930== by 0x4F16FF6: EVP_MD_CTX_create (in /usr/lib64/libcrypto.so.1.0.1e)
==3930== by 0x401231: thread_func (block_hasher.c:163)
==3930== by 0x4C2D01D: mythread_wrapper (hg_intercepts.c:221)
==3930== by 0x541F9D0: start_thread (in /lib64/libpthread-2.12.so)
==3930== by 0x75E46FF: ???
==3930== This conflicts with a previous write of size 4 by thread #2
==3930== at 0x4E98AF8: CRYPTO_malloc (in /usr/lib64/libcrypto.so.1.0.1e)
==3930== by 0x4F16FF6: EVP_MD_CTX_create (in /usr/lib64/libcrypto.so.1.0.1e)
==3930== by 0x401231: thread_func (block_hasher.c:163)
==3930== by 0x4C2D01D: mythread_wrapper (hg_intercepts.c:221)
==3930== by 0x541F9D0: start_thread (in /lib64/libpthread-2.12.so)
==3930== by 0x6BE36FF: ???
==3930== 
==3930== 
==3930== For counts of detected and suppressed errors, rerun with: -v
==3930== Use --history-level=approx or =none to gain increased speed, at
==3930== the cost of reduced accuracy of conflicting-access information
==3930== ERROR SUMMARY: 9 errors from 1 contexts (suppressed: 955 from 9) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，`EVP_MD_CTX_create`导致了数据竞赛。这是一个 OpenSSL 的 <sup id="fnref:libcrypto">[1](#fn:libcrypto)</sup> 函数，为哈希计算初始化上下文。我使用`EVP_DigestUpdate`计算每个线程中读取的块的哈希，然后在最终的`EVP_DigesFinal_ex`之后将其写入文件。所以这些 *Helgrind* 错误都和 OpenSSL 函数有关。我问自己——“libcrypto 是线程安全的吗？”。所以我用我的 google-fu，答案是——默认[**否**](http://wiki.openssl.org/index.php/Libcrypto_API#Thread_Safety)**。为了在多线程应用中使用 EVP 函数，OpenSSL 建议要么注册 2 个疯狂回调，要么使用动态锁(参见此处的)。至于我，我只是将上下文初始化包装在 pthread mutex 和[中，仅此而已](https://github.com/dzeban/block_hasher/commit/c1994f763d4fce8bb41e97af45eac6e2ad0dc3b7)。** 

```
@@ -159,9 +159,11 @@ void *thread_func(void *arg)
     gap = num_threads * block_size; // Multiply here to avoid integer overflow

     // Initialize EVP and start reading
+    pthread_mutex_lock( &mutex );
     md = EVP_sha1();
     mdctx = EVP_MD_CTX_create();
     EVP_DigestInit_ex( mdctx, md, NULL );
+    pthread_mutex_unlock( &mutex ); 
```

Enter fullscreen mode Exit fullscreen mode

如果有人知道这件事，请告诉我。

## DRD

*DRD* 是 *Valgrind* 套件中又一个可以检测线程错误的工具。它更彻底，有更多的功能，而更少的内存饥饿。

在我的例子中，它检测到了一些神秘的`pread`数据竞争。

```
==16358== Thread 3:
==16358== Conflicting load by thread 3 at 0x0563e398 size 4
==16358== at 0x5431030: pread (in /lib64/libpthread-2.12.so)
==16358== by 0x4012D9: thread_func (block_hasher.c:174)
==16358== by 0x4C33470: vgDrd_thread_wrapper (drd_pthread_intercepts.c:281)
==16358== by 0x54299D0: start_thread (in /lib64/libpthread-2.12.so)
==16358== by 0x75EE6FF: ??? 
```

Enter fullscreen mode Exit fullscreen mode

`pread`本身是线程安全的，因为它可以被多个线程调用，但是*对数据的访问*可能是不同步的。例如，你可以在一个线程中调用`pread`,而在另一个线程中调用`pwrite`,这就是你得到*数据*的地方。但是在我的例子中，数据块没有重叠，所以我不知道什么是真正的问题。

## 结论

结论非常简单——学习如何使用 Valgrind，它非常有用。

## 阅读

*   成功案例:
    *   [rsyslog 数据竞争分析](http://blog.gerhards.net/2009/01/rsyslog-data-race-analysis.html)
    *   [valgrind 和 ruby](http://blog.evanweaver.com/2008/02/05/valgrind-and-ruby/)
    *   [使用 Valgrind Massif 分析 MySQL 内存使用情况](http://sql.dzone.com/articles/profiling-mysql-memory-usage)
*   [Valgrind 的设计与实现。针对黑客、维护者和过度好奇者的详细技术说明](http://courses.cs.washington.edu/courses/cse326/05wi/valgrind-doc/mc_techdocs.html)

* * *

1.  libcrypto 是 openssl 所基于的加密函数库和原语库。**