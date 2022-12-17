# 限制程序内存

> 原文：<https://dev.to/dzeban/restricting-program-memory-3269>

前几天，我决定解决一个流行的问题:[如何对 1 MiB 中的 1 百万个整数进行排序？](///programming/external-sort.html)

但是在我开始做任何事情之前，我想——我如何将进程内存限制在 1 MiB？行得通吗？所以，这里是答案。

## 进程虚拟内存

在研究各种方法之前，您必须知道进程的虚拟内存是如何构造的。毫无疑问，你能找到的最好的文章是古斯塔沃·杜阿尔特的《记忆中的程序剖析》。他的整个博客是一个宝藏。

在阅读了 Gustavo 的文章之后，我可以提出两种限制内存的可能选择——减少虚拟地址空间和限制堆大小。

首先是限制进程的整个虚拟地址空间。这很好也很简单，但并不完全正确。我们不能将整个进程的虚拟地址空间限制为 1mb——我们将无法映射内核和库。

其次是限制*堆*的大小。这并不容易，似乎没有人试图这样做，因为唯一合理的方法是玩连接器。但是对于将可用内存限制到像 1 MiB 这样的小值来说，这是绝对正确的。

此外，我还将研究其他方法，比如通过拦截与内存管理相关的库和系统调用来监控内存消耗，以及通过仿真和沙箱来改变程序环境。

为了测试和演示，我将使用这个分配(和释放)100 MiB 的小程序`big_alloc`。

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h> 
// 1000 allocation per 100 KiB = 100 000 KiB = 100 MiB
#define NALLOCS 1000
#define ALLOC_SIZE 1024*100 // 100 KiB 
int main(int argc, const char *argv[])
{
    int i = 0;
    int **pp;
    bool failed = false;

    pp = malloc(NALLOCS * sizeof(int *));
    for(i = 0; i < NALLOCS; i++)
    {
        pp[i] = malloc(ALLOC_SIZE);
        if (!pp[i])
        {
            perror("malloc");
            printf("Failed after %d allocations\n", i);
            failed = true;
            break;
        }
        // Touch some bytes in memory to trick copy-on-write.
        memset(pp[i], 0xA, 100);
        printf("pp[%d] = %p\n", i, pp[i]);
    }

    if (!failed)
        printf("Successfully allocated %d bytes\n", NALLOCS * ALLOC_SIZE);

    for(i = 0; i < NALLOCS; i++)
    {
        if (pp[i])
            free(pp[i]);
    }
    free(pp);

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

所有的来源都在 github 上[。](https://github.com/dzeban/restrict-memory)

## ulimit

是老 unix 黑客被要求限制程序内存时能想到的第一件事。`ulimit`是一个 bash 工具，允许你限制程序资源，只是 [`setrlimit`](http://linux.die.net/man/2/setrlimit) 的接口。

我们可以设置常驻内存大小的限制。

```
$ ulimit -m 1024 
```

Enter fullscreen mode Exit fullscreen mode

现在检查:

```
$ ulimit -a
core file size (blocks, -c) 0
data seg size (kbytes, -d) unlimited
scheduling priority (-e) 0
file size (blocks, -f) unlimited
pending signals (-i) 7802
max locked memory (kbytes, -l) 64
max memory size (kbytes, -m) 1024
open files (-n) 1024
pipe size (512 bytes, -p) 8
POSIX message queues (bytes, -q) 819200
real-time priority (-r) 0
stack size (kbytes, -s) 8192
cpu time (seconds, -t) unlimited
max user processes (-u) 1024
virtual memory (kbytes, -v) unlimited
file locks (-x) unlimited 
```

Enter fullscreen mode Exit fullscreen mode

我们将限制设置为 1024 千字节(-m)，即 1 兆字节。但是当我们试图运行我们的程序时，它不会失败。将限制设置为更合理的值，比如 30 MiB，无论如何都会让我们的程序分配 100 MB。`ulimit`根本行不通。尽管将常驻集的大小设置为 1024 千字节，但我可以在顶部看到我的程序的常驻内存是 4872。

原因是 Linux 不尊重这个`man ulimit`直接告诉它:

```
ulimit [-HSTabcdefilmnpqrstuvx [limit]]
    ...
    -m The maximum resident set size (many systems do not honor this limit)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

还有根据内核尊重[的`ulimit -d`，但是因为 mmap(参见链接器章节)它仍然工作。](http://lxr.free-electrons.com/source/mm/mmap.c?v=3.16#L290)

## QEMU

当您想要修改程序环境时，QEMU 是完成这类任务的自然方式。它有`-R`选项来限制虚拟地址空间。但是就像我前面说的，你不能将地址空间限制为很小的值——没有空间来映射 libc 和内核。

看:

```
$ qemu-i386 -R 1048576 ./big_alloc
big_alloc: error while loading shared libraries: libc.so.6: failed to map segment from shared object: Cannot allocate memory 
```

Enter fullscreen mode Exit fullscreen mode

这里，`-R 1048576`为客户虚拟地址空间保留 1 MiB。

对于整个虚拟地址空间，我们必须设置一些更合理的东西，比如 20 MB。看:

```
$ qemu-i386 -R 20M ./big_alloc
malloc: Cannot allocate memory
Failed after 100 allocations 
```

Enter fullscreen mode Exit fullscreen mode

它在 100 次分配(10 MB)后成功地使 <sup id="fnref:success-fail">[1](#fn:success-fail)</sup> 失败。

因此，QEMU 是限制程序内存大小的第一个赢家，尽管您必须使用`-R`值来获得正确的限制。

## 容器

QEMU 之后的另一个选择是在容器中启动应用程序，限制其资源。为此，您有几种选择:

1.  用花式高级 *docker* 。
2.  使用 *lxc* 包中的常规用户模式工具。
3.  使用 *libvirt* 编写您自己的脚本。
4.  说出它的名字…

但毕竟，资源会受到名为 *cgroups* 的原生 Linux 子系统的限制。你可以尝试直接戳它，但我建议使用 *lxc* 。我想使用 docker，但它只能在 64 位机器上工作，我的机器是小型英特尔凌动上网本 i386。

好吧，快速信息。 *LXC* 是 *LinuX 容器*。它是一个用户空间工具和库的集合，用于管理内核设施以创建容器——为应用程序或整个系统提供隔离和安全的环境。

提供这种环境的内核设施有:

*   控制组(群组)
*   内核名称空间
*   克鲁特
*   内核功能
*   SELinux，出现
*   Seccomp 政策

你可以在[官方网站](https://linuxcontainers.org/)，在[作者的博客](https://www.stgraber.org/2013/12/20/lxc-1-0-blog-post-series/)以及整个互联网上找到很好的文档。

要简单地在容器中运行应用程序，您必须向`lxc-execute`提供配置，在那里您将配置您的容器。每一个理智的人，都应该从`/usr/share/doc/lxc/examples`中的例子说起。手册页建议从`lxc-macvlan.conf`开始。好，我们这样做:

```
# cp /usr/share/doc/lxc/examples/lxc-macvlan.conf lxc-my.conf
# lxc-execute -n foo -f ./lxc-my.conf ./big_alloc
Successfully allocated 102400000 bytes 
```

Enter fullscreen mode Exit fullscreen mode

有用！

现在让我们限制内存。这就是我们小组的目的。LXC 允许您通过设置内存限制为容器的 cgroup 配置内存子系统。

您可以在这本[精美的 RedHat 手册](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Resource_Management_Guide/sec-memory.html)中找到内存子系统可用的可调参数。我发现了 2:

*   `memory.limit_in_bytes`–设置最大用户内存量(包括文件缓存)
*   `memory.memsw.limit_in_bytes`–设置内存和交换空间使用总量的最大值

以下是我添加到 *lxc-my.conf* :

```
lxc.cgroup.memory.limit_in_bytes = 2M
lxc.cgroup.memory.memsw.limit_in_bytes = 2M 
```

Enter fullscreen mode Exit fullscreen mode

再次启动:

```
# lxc-execute -n foo -f ./lxc-my.conf ./big_alloc
# 
```

Enter fullscreen mode Exit fullscreen mode

什么都没发生，看起来像是小内存的方式。让我们试着从容器中的外壳启动它。

```
# lxc-execute -n foo -f ./lxc-my.conf /bin/bash
# 
```

Enter fullscreen mode Exit fullscreen mode

看来 bash 发射失败了。让我们试试`/bin/sh` :

```
# lxc-execute -n foo -f ./lxc-my.conf -l DEBUG -o log /bin/sh
sh-4.2# ./dev/big_alloc/big_alloc 
Killed 
```

Enter fullscreen mode Exit fullscreen mode

耶！我们可以在`dmesg` :
中看到这种美好的杀戮行为

```
[15447.035569] big_alloc invoked oom-killer: gfp_mask=0xd0, order=0, oom_score_adj=0
...
[15447.035779] Task in /lxc/foo
[15447.035785] killed as a result of limit of 
[15447.035789] /lxc/foo

[15447.035795] memory: usage 3072kB, limit 3072kB, failcnt 127
[15447.035800] memory+swap: usage 3072kB, limit 3072kB, failcnt 0
[15447.035805] kmem: usage 0kB, limit 18014398509481983kB, failcnt 0
[15447.035808] Memory cgroup stats for /lxc/foo: cache:32KB rss:3040KB rss_huge:0KB mapped_file:0KB writeback:0KB swap:0KB inactive_anon:1588KB active_anon:1448KB inactive_file:16KB active_file:16KB unevictable:0KB
[15447.035836] [pid] uid tgid total_vm rss nr_ptes swapents oom_score_adj name
[15447.035963] [9225] 0 9225 942 308 10 0 0 init.lxc
[15447.035971] [9228] 0 9228 833 698 6 0 0 sh
[15447.035978] [9252] 0 9252 16106 843 36 0 0 big_alloc
[15447.035983] Memory cgroup out of memory: Kill process 9252 (big_alloc) score 1110 or sacrifice child
[15447.035990] Killed process 9252 (big_alloc) total-vm:64424kB, anon-rss:2396kB, file-rss:976kB 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们还没有看到来自`big_alloc`的关于 malloc 失败和我们能够获得多少内存的错误消息，但我认为我们已经通过容器技术成功地限制了内存，现在可以停止了。

## [左](#linker)

现在，让我们尝试修改限制堆可用空间的二进制映像。

链接是构建程序的最后一部分，它意味着使用链接器和链接器脚本。链接器脚本是对内存中程序段及其属性的描述。

下面是一个简单的链接器脚本:

```
ENTRY(main)

SECTIONS
{
  . = 0x10000;
  .text : { *(.text) }
  . = 0x8000000;
  .data : { *(.data) }
  .bss : { *(.bss) }
} 
```

Enter fullscreen mode Exit fullscreen mode

圆点是*当前的*位置。该脚本告诉我们的是，`.text`部分从地址 0x10000 开始，然后从 0x8000000 开始，我们有两个后续部分`.data`和`.bss`。切入点是`main`。

不错，但它不会为任何有用的应用程序工作。原因是你在 C 程序中写的`main`函数并不是第一个被调用的函数。有大量的初始化和清理代码。该代码随 C 运行时一起提供(也简称为 *crt* )，并传播到 *crt#中。o`/usr/lib`中的*库。

如果你用`-v`选项启动`gcc`，你可以看到确切的细节。你会看到，首先它调用`cc1`并创建程序集，然后用`as`将其翻译成目标文件，最后用`collect2`将 ELF 文件中的所有内容合并。那个`collect2`是`ld`包装。它需要你的目标文件和 5 个额外的库来创建最终的二进制映像:

*   `/usr/lib/gcc/i686-redhat-linux/4.8.3/../../../crt1.o`
*   `/usr/lib/gcc/i686-redhat-linux/4.8.3/../../../crti.o`
*   `/usr/lib/gcc/i686-redhat-linux/4.8.3/crtbegin.o`
*   `/tmp/ccEZwSgF.o` `<--`这是我们的程序目标文件
*   `/usr/lib/gcc/i686-redhat-linux/4.8.3/crtend.o`
*   `/usr/lib/gcc/i686-redhat-linux/4.8.3/../../../crtn.o`

这真的很**复杂，所以我将修改默认的链接器脚本，而不是写我自己的脚本。获取默认链接器脚本，将`-Wl,-verbose`传递给`gcc` :** 

```
gcc big_alloc.c -o big_alloc -Wl,-verbose 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们想办法修改它。让我们看看我们的二进制文件是如何默认构建的。编译并查找`.data`部分地址。这里是`objdump -h
big_alloc`输出

```
Sections:
Idx Name Size VMA LMA File off Algn
...
12 .text 000002e4 080483e0 080483e0 000003e0 2**4
                 CONTENTS, ALLOC, LOAD, READONLY, CODE
...
23 .data 00000004 0804a028 0804a028 00001028 2**2
                 CONTENTS, ALLOC, LOAD, DATA
24 .bss 00000004 0804a02c 0804a02c 0000102c 2**2
                 ALLOC 
```

Enter fullscreen mode Exit fullscreen mode

`.text`、`.data`和`.bss`段位于 128 MiB 附近。

现在，让我们在*广发* :
的帮助下看看堆栈在哪里

```
[restrict-memory]$ gdb big_alloc
...
Reading symbols from big_alloc...done.
(gdb) break main
Breakpoint 1 at 0x80484fa: file big_alloc.c, line 12.
(gdb) r
Starting program: /home/avd/dev/restrict-memory/big_alloc 

Breakpoint 1, main (argc=1, argv=0xbffff164) at big_alloc.c:12
12 int i = 0;
Missing separate debuginfos, use: debuginfo-install glibc-2.18-16.fc20.i686
(gdb) info registers 
eax 0x1 1
ecx 0x9a8fc98f -1701852785
edx 0xbffff0f4 -1073745676
ebx 0x42427000 1111650304
esp 0xbffff0a0 0xbffff0a0
ebp 0xbffff0c8 0xbffff0c8
esi 0x0 0
edi 0x0 0
eip 0x80484fa 0x80484fa <main+10>
eflags 0x286 [PF SF IF]
cs 0x73 115
ss 0x7b 123
ds 0x7b 123
es 0x7b 123
fs 0x0 0
gs 0x33 51 
```

Enter fullscreen mode Exit fullscreen mode

`esp`指向接近 3 GiB 的`0xbffff0a0`。所以我们有大约 2.9 GiB 的堆。

在现实世界中，栈顶地址是随机的，例如，您可以在
的输出中看到它

```
# cat /proc/self/maps 
```

Enter fullscreen mode Exit fullscreen mode

众所周知，heap 是从`.data`的末端向堆栈方向增长的。**如果我们把`.data`部分移到尽可能高的地址会怎么样？**

让我们将数据段 2 MiB 放在堆栈之前。取栈顶，减去 2 MiB:

```
0xbffff0a0 - 0x200000 = 0xbfdff0a0 
```

Enter fullscreen mode Exit fullscreen mode

现在将从`.data`开始的所有部分移动到地址:

```
. = 0xbfdff0a0
.data :
{
  *(.data .data.* .gnu.linkonce.d.*)
  SORT(CONSTRUCTORS)
} 
```

Enter fullscreen mode Exit fullscreen mode

编译一下:

```
$ gcc big_alloc.c -o big_alloc -Wl,-T hack.lst 
```

Enter fullscreen mode Exit fullscreen mode

`-Wl`是链接器的选项，`-T hack.lst`是链接器选项本身。它告诉链接器使用`hack.lst`作为链接器脚本。

现在，如果我们看标题，我们会看到:

```
Sections:
Idx Name Size VMA LMA File off Algn

 ...

 23 .data 00000004 bfdff0a0 bfdff0a0 000010a0 2**2
                  CONTENTS, ALLOC, LOAD, DATA
 24 .bss 00000004 bfdff0a4 bfdff0a4 000010a4 2**2
                  ALLOC 
```

Enter fullscreen mode Exit fullscreen mode

但不管怎样，它成功地分配了。怎么做？这真的很棒。当我试图查看 malloc 返回的指针值时，我看到分配开始于`0xbf8b7000`之类的`.data`部分末尾的某个地方，持续一段时间增加指针，然后将指针重置为 _lower_address 之类的`0xb7676000`。从这个地址开始，它将分配一段时间，增加指针，然后再次将指针重置到更低的地址，如`0xb5e76000`。最终它看起来就像是堆成长下来！

但是如果你想一分钟，这并不奇怪。我检查了一些 [glibc 源](http://code.metager.de/source/xref/gnu/glibc/malloc/malloc.c#sysmalloc)，发现当`brk`失败时，它将使用`mmap`代替。因此，glibc 要求内核映射一些页面，内核发现该进程在虚拟内存空间中有许多漏洞，并从该空间为 glibc 映射页面，最后 glibc 从该页面返回指针。

在`strace`下运行`big_alloc`证实理论。只看正常的二进制:

```
brk(0) = 0x8135000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb77df000
mmap2(NULL, 95800, PROT_READ, MAP_PRIVATE, 3, 0) = 0xb77c7000
mmap2(0x4226d000, 1825436, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x4226d000
mmap2(0x42425000, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b8000) = 0x42425000
mmap2(0x42428000, 10908, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x42428000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb77c6000
mprotect(0x42425000, 8192, PROT_READ) = 0
mprotect(0x8049000, 4096, PROT_READ) = 0
mprotect(0x42269000, 4096, PROT_READ) = 0
munmap(0xb77c7000, 95800) = 0
brk(0) = 0x8135000
brk(0x8156000) = 0x8156000
brk(0) = 0x8156000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb77de000
brk(0) = 0x8156000
brk(0x8188000) = 0x8188000
brk(0) = 0x8188000
brk(0x81ba000) = 0x81ba000
brk(0) = 0x81ba000
brk(0x81ec000) = 0x81ec000
...
brk(0) = 0x9c19000
brk(0x9c4b000) = 0x9c4b000
brk(0) = 0x9c4b000
brk(0x9c7d000) = 0x9c7d000
brk(0) = 0x9c7d000
brk(0x9caf000) = 0x9caf000
...
brk(0) = 0xe29c000
brk(0xe2ce000) = 0xe2ce000
brk(0) = 0xe2ce000
brk(0xe300000) = 0xe300000
brk(0) = 0xe300000
brk(0) = 0xe300000
brk(0x8156000) = 0x8156000
brk(0) = 0x8156000
+++ exited with 0 +++ 
```

Enter fullscreen mode Exit fullscreen mode

现在是修改后的二进制

```
brk(0) = 0xbf896000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb778f000
mmap2(NULL, 95800, PROT_READ, MAP_PRIVATE, 3, 0) = 0xb7777000
mmap2(0x4226d000, 1825436, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x4226d000
mmap2(0x42425000, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b8000) = 0x42425000
mmap2(0x42428000, 10908, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x42428000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7776000
mprotect(0x42425000, 8192, PROT_READ) = 0
mprotect(0x8049000, 4096, PROT_READ) = 0
mprotect(0x42269000, 4096, PROT_READ) = 0
munmap(0xb7777000, 95800) = 0
brk(0) = 0xbf896000
brk(0xbf8b7000) = 0xbf8b7000
brk(0) = 0xbf8b7000
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb778e000
brk(0) = 0xbf8b7000
brk(0xbf8e9000) = 0xbf8e9000
brk(0) = 0xbf8e9000
brk(0xbf91b000) = 0xbf91b000
brk(0) = 0xbf91b000
brk(0xbf94d000) = 0xbf94d000
brk(0) = 0xbf94d000
brk(0xbf97f000) = 0xbf97f000
...
brk(0) = 0xbff8e000
brk(0xbffc0000) = 0xbffc0000
brk(0) = 0xbffc0000
brk(0xbfff2000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7676000
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7576000
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7476000
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7376000
...
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb1c76000
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb1b76000
brk(0) = 0xbffc0000
brk(0xbfffa000) = 0xbffc0000
mmap2(NULL, 1048576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb1a76000
brk(0) = 0xbffc0000
brk(0) = 0xbffc0000
brk(0) = 0xbffc0000
...
brk(0) = 0xbffc0000
brk(0) = 0xbffc0000
brk(0) = 0xbffc0000
+++ exited with 0 +++ 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，将`.data`部分上移至堆栈(从而减少堆的空间)是没有意义的，因为内核将从虚拟内存空白区域为 malloc 映射页面。

## 沙盒

限制程序内存的另一种方法是沙盒。与仿真的不同之处在于，我们不是真的在仿真任何东西，而是在程序行为中跟踪和控制某些东西。当你有某种恶意软件并且需要在不损害你的系统的情况下分析它时，沙盒通常被用于安全研究。

我提出了几种沙盒方法，并实现了最有前途的方法。

### LD _ 预载技巧

`LD_PRELOAD`是一个特殊的环境变量，当它被设置时，将使动态链接器在使用任何其他库(包括 libc 库)之前使用“预加载的”库。它被用在很多场景中，从调试到沙盒。

这种伎俩也被一些恶意软件臭名昭著地[使用。](http://blog.malwaremustdie.org/2014/05/elf-shared-so-dynamic-library-malware.html)

我写了一个简单的内存管理沙箱，它拦截`malloc` / `free`调用，计算内存使用量，如果超出内存限制，就返回`ENOMEM`。

为了做到这一点，我用自己的`malloc` / `free`包装器编写了一个共享库，当`free`被调用时，它将按照`malloc`的大小递增计数器，并递减计数器。当运行测试中的应用程序时，这个库被预加载了`LD_PRELOAD`。

这是我的 malloc 实现。

```
void *malloc(size_t size)
{
    void *p = NULL;

    if (libc_malloc == NULL) 
        save_libc_malloc();

    if (mem_allocated <= MEM_THRESHOLD)
    {
        p = libc_malloc(size);
    }
    else
    {
        errno = ENOMEM;
        return NULL;
    }

    if (!no_hook) 
    {
        no_hook = 1;
        account(p, size);
        no_hook = 0;
    }

    return p;
} 
```

Enter fullscreen mode Exit fullscreen mode

`libc_malloc`是指向 libc 中原始`malloc`的指针。`no_hook`是一个线程本地的标志。它被用来在 malloc 钩子中使用 malloc，避免递归调用——这个想法来自于 [Tetsuyuki Kobayashi 的演讲](http://www.slideshare.net/tetsu.koba/tips-of-malloc-free)。

`malloc`被 [uthash](http://troydhanson.github.io/uthash/) 哈希表库隐式使用在`account`函数中。为什么要使用哈希表？这是因为当你调用`free`时，你只传递给它一个指针，而在`free`中，你不知道已经分配了多少内存。所以我有一个散列表，指针作为键，分配的大小作为值。下面是我在 malloc 上做的事情:

```
struct malloc_item *item, *out;

item = malloc(sizeof(*item));
item->p = ptr;
item->size = size;

HASH_ADD_PTR(HT, p, item);

mem_allocated += size;

fprintf(stderr, "Alloc: %p -> %zu\n", ptr, size); 
```

Enter fullscreen mode Exit fullscreen mode

`mem_allocated`是与`malloc`中的阈值进行比较的静态变量。

当调用`free`时，这里发生了什么:

```
struct malloc_item *found;

HASH_FIND_PTR(HT, &ptr, found);
if (found)
{
    mem_allocated -= found->size;
    fprintf(stderr, "Free: %p -> %zu\n", found->p, found->size);
    HASH_DEL(HT, found);
    free(found);
}
else
{
    fprintf(stderr, "Freeing unaccounted allocation %p\n", ptr);
} 
```

Enter fullscreen mode Exit fullscreen mode

没错，就是递减`mem_allocated`。就这么简单。

但真正酷的是，它的工作坚如磐石 <sup id="fnref:own-ld-preload">[2](#fn:own-ld-preload)</sup> 。

```
[restrict-memory]$ LD_PRELOAD=./libmemrestrict.so ./big_alloc
pp[0] = 0x25ac210
pp[1] = 0x25c5270
pp[2] = 0x25de2d0
pp[3] = 0x25f7330
pp[4] = 0x2610390
pp[5] = 0x26293f0
pp[6] = 0x2642450
pp[7] = 0x265b4b0
pp[8] = 0x2674510
pp[9] = 0x268d570
pp[10] = 0x26a65d0
pp[11] = 0x26bf630
pp[12] = 0x26d8690
pp[13] = 0x26f16f0
pp[14] = 0x270a750
pp[15] = 0x27237b0
pp[16] = 0x273c810
pp[17] = 0x2755870
pp[18] = 0x276e8d0
pp[19] = 0x2787930
pp[20] = 0x27a0990
malloc: Cannot allocate memory
Failed after 21 allocations 
```

Enter fullscreen mode Exit fullscreen mode

该库的完整源代码在 github 上的

所以，LD_PRELOAD 是限制内存的好方法！

### ptrace

`ptrace`是另一个可以用来构建内存沙箱的特性。`ptrace`是一个系统调用，允许你控制另一个进程的执行。它内置于各种 POSIX 操作系统中，当然包括 Linux。

`ptrace`是像 [*strace*](http://sourceforge.net/p/strace/code/ci/master/tree/strace.c#l343) ， [*ltrace*](http://anonscm.debian.org/cgit/collab-maint/ltrace.git/tree/sysdeps/linux-gnu/trace.c#n78) 这样的 tracers 的基础，几乎每个沙盒软件像 [*systrace*](http://www.citi.umich.edu/u/provos/systrace/) ， [*sydbox*](https://github.com/psychoschlumpf/sydbox) ， [*mbox*](http://pdos.csail.mit.edu/mbox/) 以及所有调试器包括 [*gdb*](https://sourceware.org/git/gitweb.cgi?p=binutils-gdb.git;a=blob;f=gdb/inf-ptrace.c;h=6eb8080242349296e43dcc19df4a0896e6093fa8;hb=HEAD) 本身。

我用`ptrace`构建了一个自定义工具。它跟踪`brk`调用，寻找初始程序中断值和下一个`brk`调用设置的新值之间的距离。

这个工具分叉成两个进程。父进程是 tracer，子进程是 tracee。在子进程中，我先调用`ptrace(PTRACE_TRACEME)`，然后调用`execv`。在父进程中，我使用`ptrace(PTRACE_SYSCALL)`来停止 syscall 并过滤来自子进程的`brk`调用，然后使用另一个`ptrace(PTRACE_SYSCALL)`来获得`brk`返回值。

当`brk`超过阈值时，我将`-ENOMEM`设置为`brk`返回值。这是在`eax`寄存器中设置的，所以我用`ptrace(PTRACE_SETREGS)`将其覆盖。这里是肉的部分:

```
// Get return value
if (!syscall_trace(pid, &state))
{
    dbg("brk return: 0x%08X, brk_start 0x%08X\n", state.eax, brk_start);

    if (brk_start) // We have start of brk
    {
        diff = state.eax - brk_start;

        // If child process exceeded threshold 
        // replace brk return value with -ENOMEM
        if (diff > THRESHOLD || threshold) 
        {
            dbg("THRESHOLD!\n");
            threshold = true;
            state.eax = -ENOMEM;
            ptrace(PTRACE_SETREGS, pid, 0, &state);
        }
        else
        {
            dbg("diff 0x%08X\n", diff);
        }
    }
    else
    {
        dbg("Assigning 0x%08X to brk_start\n", state.eax);
        brk_start = state.eax;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，我拦截`mmap` / `mmap2`调用，因为 libc 足够聪明，可以在`brk`失败时调用它。因此，当我超过阈值，看到`mmap`调用，我只是失败的`ENOMEM`。

有用！

```
[restrict-memory]$ ./ptrace-restrict ./big_alloc
pp[0] = 0x8958fb0
pp[1] = 0x8971fb8
pp[2] = 0x898afc0
pp[3] = 0x89a3fc8
pp[4] = 0x89bcfd0
pp[5] = 0x89d5fd8
pp[6] = 0x89eefe0
pp[7] = 0x8a07fe8
pp[8] = 0x8a20ff0
pp[9] = 0x8a39ff8
pp[10] = 0x8a53000
pp[11] = 0x8a6c008
pp[12] = 0x8a85010
pp[13] = 0x8a9e018
pp[14] = 0x8ab7020
pp[15] = 0x8ad0028
pp[16] = 0x8ae9030
pp[17] = 0x8b02038
pp[18] = 0x8b1b040
pp[19] = 0x8b34048
pp[20] = 0x8b4d050
malloc: Cannot allocate memory
Failed after 21 allocations 
```

Enter fullscreen mode Exit fullscreen mode

但是…我真的不喜欢。它是 ABI 特有的，也就是说，它必须在 64 位机器上使用`rax`而不是`eax`，所以要么我制作该工具的不同版本，要么使用`#ifdef`来应对 ABI 差异，要么让你用`-m32`选项来构建它。但那是不可用的。此外，它可能无法在其他类似 POSIX 的系统上工作，因为它们可能有不同的 ABI。

### 其他

还有其他的事情可以尝试，但我以不同的理由拒绝了:

*   [**malloc 钩子**](http://www.gnu.org/software/libc/manual/html_node/Hooks-for-Malloc.html) 。如手册页所说，已被弃用，所以我没有尝试。
*   [**与`prctl`同`PR_SET_MM_START_BRK`**](http://man7.org/linux/man-pages/man2/prctl.2.html) 。这可能行得通，但正如在 [seccomp 过滤内核文档](http://lxr.free-electrons.com/source/Documentation/prctl/seccomp_filter.txt)中所说，它不是沙箱，而是一种“最小化暴露内核表面的机制”。所以我估计会比手动用 ptrace 更尴尬。虽然我可能会看看它。
*   [**【lib virt】沙盒**](http://sandbox.libvirt.org/quickstart/) 。不，它只是 lxc 和 qemu 的包装。
*   [**SELinux 沙盒**](http://linux.die.net/man/8/sandbox) 。没有。尽管它使用了 cgroup，但还是不起作用。

## 重述

最后，我想总结一下:

*   限制内存有很多种方法:
    *   使用 ulimit 和 cgroup 进行资源限制
    *   在类似 QEMU 的模拟器下运行
    *   使用 LD_PRELOAD 和 ptrace 的沙箱
    *   修改二进制图像中的段。
*   但并不是所有人都在工作
    *   `ulimit`不起作用。
    *   `cgroup`有点作品——崩溃的应用
    *   仿真工程-崩溃应用
    *   `LD_PRELOAD`作品惊艳！
    *   `ptrace`工作足够好，但 ABI 依赖
    *   链接器魔法不起作用是因为巧妙的 libc 调用了`mmap`。

## 参考文献

1.  [又是古斯塔沃·杜阿尔特的文章。](http://duartes.org/gustavo/blog/post/anatomy-of-a-program-in-memory/)
2.  在 Linux 中限制一个程序的时间和内存消耗。
3.  [Linux 沙盒](http://stackoverflow.com/questions/4249063/run-an-untrusted-c-program-in-a-sandbox-in-linux-that-prevents-it-from-opening-f)

* * *

1.  我想我刚刚为 QA 人员发明了一个新术语。<sup>【返回】</sup>
2.  除非应用本身使用 LD _ PRELOAD:-\<sup>【return】</sup>