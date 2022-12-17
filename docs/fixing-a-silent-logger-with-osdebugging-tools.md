# 用操作系统调试工具修复静默记录器

> 原文：<https://dev.to/tomhoule/fixing-a-silent-logger-with-osdebugging-tools>

在最近发布的 0.2 版本的 Papers 中，我们想要的一个特性是调试上传到 S3 桶中的生成文档的输出。我们在调试档案中需要的主要内容之一是来自服务本身的日志——这意味着在每个文档生成的基础上为我们的日志提供第二个接收器，最好是具有更高的日志级别。幸运的是，这在 [slog](https://github.com/slog-rs/slog) 2.0 中以一种无样板的方式成为可能，因为记录器现在也是接收器。

所以我们决定更新到 slog 2 并重新设计我们的日志配置，这只是几行代码。甚至在 slog 周围有一个漂亮的高级包装器，叫做 [sloggers](https://github.com/sile/sloggers) ，用于以漂亮的颜色注销文件或终端。

它进行得非常顺利，结果也无可挑剔，但是不能简单地将登录到终端的应用程序容器化:在我们的测试部署中，服务正在运行，但是绝对没有终端输出。

在确认发布的二进制文件确实在本地登录了同一个 docker 映像，并调查了几个疑点之后，包括 fluentd 和 slog 在发布版本中的不同行为(这是[记录的行为](https://docs.rs/slog/2.0.9/slog/#notable-details)，我求助于我最喜欢的调试工具之一: [strace](https://en.wikipedia.org/wiki/Strace) 。

运行你的程序并打印任何系统调用到终端，允许你检查所有的 IO 操作。对于论文，你会得到那样的东西(节选):

```
open("/usr/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340\5\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1985472, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6ca3d000
mmap(NULL, 3823824, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f5f6b57b000
mprotect(0x7f5f6b718000, 2093056, PROT_NONE) = 0
mmap(0x7f5f6b917000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x19c000) = 0x7f5f6b917000
mmap(0x7f5f6b91d000, 14544, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f5f6b91d000
close(3)                                = 0
open("/usr/lib/libm.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0`]\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1120216, ...}) = 0
mmap(NULL, 3215384, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f5f6b269000
mprotect(0x7f5f6b37a000, 2093056, PROT_NONE) = 0
mmap(0x7f5f6b579000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x110000) = 0x7f5f6b579000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6ca3a000
arch_prctl(ARCH_SET_FS, 0x7f5f6ca3a8c0) = 0
mprotect(0x7f5f6b917000, 16384, PROT_READ) = 0
mprotect(0x7f5f6b579000, 4096, PROT_READ) = 0
mprotect(0x7f5f6bb36000, 4096, PROT_READ) = 0
mprotect(0x7f5f6bd50000, 4096, PROT_READ) = 0
mprotect(0x7f5f6bf5c000, 4096, PROT_READ) = 0
mprotect(0x7f5f6c160000, 4096, PROT_READ) = 0
mprotect(0x7f5f6c5b7000, 122880, PROT_READ) = 0
mprotect(0x7f5f6c842000, 20480, PROT_READ) = 0
mprotect(0x55873cd6f000, 1167360, PROT_READ) = 0
mprotect(0x7f5f6ca6f000, 4096, PROT_READ) = 0
munmap(0x7f5f6ca41000, 185208)          = 0
set_tid_address(0x7f5f6ca3ab90)         = 13360
set_robust_list(0x7f5f6ca3aba0, 24)     = 0
rt_sigaction(SIGRTMIN, {sa_handler=0x7f5f6bb3d740, sa_mask=[], sa_flags=SA_RESTORER|SA_SIGINFO, sa_restorer=0x7f5f6bb497e0}, NULL, 8) = 0
rt_sigaction(SIGRT_1, {sa_handler=0x7f5f6bb3d7e0, sa_mask=[], sa_flags=SA_RESTORER|SA_RESTART|SA_SIGINFO, sa_restorer=0x7f5f6bb497e0}, NULL, 8) = 0
rt_sigprocmask(SIG_UNBLOCK, [RTMIN RT_1], NULL, 8) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
readlink("/etc/malloc.conf", 0x7ffcda2ec870, 4096) = -1 ENOENT (No such file or directory)
brk(NULL)                               = 0x55873d340000
mmap(NULL, 2097152, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6b069000
munmap(0x7f5f6b069000, 2097152)         = 0
mmap(NULL, 4190208, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6ae6a000
munmap(0x7f5f6ae6a000, 1662976)         = 0
munmap(0x7f5f6b200000, 430080)          = 0
open("/sys/devices/system/cpu/online", O_RDONLY|O_CLOEXEC) = 3
read(3, "0-3\n", 8192)                  = 4
close(3)                                = 0
rt_sigaction(SIGPIPE, {sa_handler=SIG_IGN, sa_mask=[PIPE], sa_flags=SA_RESTORER|SA_RESTART, sa_restorer=0x7f5f6b5ae940}, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=0}, 8) = 0
mmap(NULL, 2097152, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6ae00000
open("/proc/self/maps", O_RDONLY|O_CLOEXEC) = 3
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
fstat(3, {st_mode=S_IFREG|0444, st_size=0, ...}) = 0
read(3, "55873c5e7000-55873cb6f000 r-xp 0"..., 1024) = 1024
read(3, "libc-2.25.so\n7f5f6b917000-7f5f6b"..., 1024) = 1024
read(3, "pthread-2.25.so\n7f5f6bd52000-7f5"..., 1024) = 1024
read(3, "so.1.1\n7f5f6c5b7000-7f5f6c5d5000"..., 1024) = 1024
read(3, "000-7ffcda2f0000 rw-p 00000000 0"..., 1024) = 316
close(3)                                = 0
sched_getaffinity(13360, 32, [0, 1, 2, 3]) = 16
mmap(0x7ffcd9af0000, 4096, PROT_NONE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7ffcd9af0000
rt_sigaction(SIGSEGV, {sa_handler=0x55873ca20260, sa_mask=[], sa_flags=SA_RESTORER|SA_ONSTACK|SA_SIGINFO, sa_restorer=0x7f5f6bb497e0}, NULL, 8) = 0
rt_sigaction(SIGBUS, {sa_handler=0x55873ca20260, sa_mask=[], sa_flags=SA_RESTORER|SA_ONSTACK|SA_SIGINFO, sa_restorer=0x7f5f6bb497e0}, NULL, 8) = 0
sigaltstack(NULL, {ss_sp=NULL, ss_flags=SS_DISABLE, ss_size=0}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5f6ca6d000
sigaltstack({ss_sp=0x7f5f6ca6d000, ss_flags=0, ss_size=8192}, NULL) = 0
getcwd("/home/tom/src/papers", 512)     = 21
open("/home/tom/src/papers/.env", O_RDONLY|O_CLOEXEC) = 3
ioctl(3, FIOCLEX)                       = 0
read(3, "PAPERS_AWS_ACCESS_KEY=<REDACTED>"..., 8192) = 228
getrandom("", 0, GRND_NONBLOCK)         = 0
getrandom("\x3d\xfd\x3c\x08\x48\x6d\x03\x13", 8, GRND_NONBLOCK) = 8
getrandom("\x80\x11\x02\x43\x2e\xa6\x6f\x1b", 8, GRND_NONBLOCK) = 8
read(3, "", 8192)                       = 0
close(3)                                = 0
stat("/home/tom/.terminfo", 0x7ffcda2eb7d0) = -1 ENOENT (No such file or directory)
stat("/etc/terminfo", 0x7ffcda2eb7d0)   = -1 ENOENT (No such file or directory)
stat("/lib/terminfo", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0
stat("/lib/terminfo/s/screen", {st_mode=S_IFREG|0644, st_size=1587, ...}) = 0
open("/lib/terminfo/s/screen", O_RDONLY|O_CLOEXEC) = 3
ioctl(3, FIOCLEX)                       = 0
read(3, "\32\1*\0+\0\20\0i\1z\2screen|VT 100/ANSI X"..., 8192) = 1587
close(3)                                = 0
stat("/home/tom/.terminfo", 0x7ffcda2eb790) = -1 ENOENT (No such file or directory)
stat("/etc/terminfo", 0x7ffcda2eb790)   = -1 ENOENT (No such file or directory)
stat("/lib/terminfo", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0
stat("/lib/terminfo/s/screen", {st_mode=S_IFREG|0644, st_size=1587, ...}) = 0
open("/lib/terminfo/s/screen", O_RDONLY|O_CLOEXEC) = 3
ioctl(3, FIOCLEX)                       = 0
read(3, "\32\1*\0+\0\20\0i\1z\2screen|VT 100/ANSI X"..., 8192) = 1587
close(3)                                = 0
ioctl(1, TCGETS, {B38400 opost isig icanon echo ...}) = 0
futex(0x7f5f6c161048, FUTEX_WAKE_PRIVATE, 2147483647) = 0
mmap(NULL, 2101248, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_STACK, -1, 0) = 0x7f5f6abff000
mprotect(0x7f5f6abff000, 4096, PROT_NONE) = 0
clone(child_stack=0x7f5f6adfee30, flags=CLONE_VM|CLONE_FS|CLONE_FILES|CLONE_SIGHAND|CLONE_THREAD|CLONE_SYSVSEM|CLONE_SETTLS|CLONE_PARENT_SETTID|CLONE_CHILD_CLEARTID, parent_tidptr=0x7f5f6adff9d0, tls=0x7f5f6adff700, child_tidptr=0x7f5f6adff9d0) = 13361
epoll_create1(EPOLL_CLOEXEC)            = 3
pipe2([4, 5], O_NONBLOCK|O_CLOEXEC)     = 0
epoll_ctl(3, EPOLL_CTL_ADD, 4, {EPOLLIN|EPOLLET, {u32=4294967295, u64=18446744073709551615}}) = 0
socket(AF_INET, SOCK_STREAM|SOCK_CLOEXEC, IPPROTO_IP) = 6
setsockopt(6, SOL_SOCKET, SO_REUSEADDR, [1], 4) = 0 
```

Enter fullscreen mode Exit fullscreen mode

一开始看起来很可怕，但是你绝对不需要知道每个系统调用做了什么来从中获得洞察力(如果你想知道，它们都有联机帮助页，所以你可以去`man futex`)。

你可以线性地跟随程序，当我们在这个 strace 输出中调用`config.rs` 中的`dotenv`时，很容易发现像[这样的东西，它是:](https://github.com/store2be/pape-rs/blob/1f4aeb791f14662ce5bcfe84aa17e4e58a7f3bbe/src/config.rs#L60) 

```
open("/home/tom/src/papers/.env", O_RDONLY|O_CLOEXEC) = 3 
```

Enter fullscreen mode Exit fullscreen mode

在跟踪的末尾，我们还有服务器开始监听的时刻(它绑定到一个 TCP 套接字)。tokio 事件循环使用的是`epoll`,所以我们的日志配置在 dotenv 之前和之后进行。

中间的许多调用是为了读取 terminfo 数据库，以计算出终端的能力。

这在我们的 kubernetes 集群的容器内的 strace 输出中是没有的。

...

就是这样:当我们设置环境变量`TERM`时，我们的日志输出回来了。

这是因为当没有指定时，kubernetes 不会给容器分配一个 TTY，所以没有设置`TERM`,并且因为没有它 sloggers 不会设置终端输出，所以不会打印任何东西。

我们的解决方案是将`TERM`设置为`screen`，尽管它也可以设置为`xterm`或`dumb`。另一个可能的解决方案是配置部署，为容器分配一个 TTY(类似于`docker run`和`docker exec`中的`-t`标志)，但是对于简单的日志记录来说，这不是必需的。

这篇文章证明了操作系统调试工具尤其是 strace 的有用性。Julia Evans 发表了一系列关于 Linux 跟踪工具的文章，我想把它们推荐给任何编写服务器端软件的人:

*   一本关于 strace 的杂志
*   [Linux 跟踪系统以及它们如何组合在一起](https://jvns.ca/blog/2017/07/05/linux-tracing-systems/)
*   跟踪你的内核函数

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*