# 文件复制是如何工作的

> 原文：<https://dev.to/samwho/how-does-file-copying-work-2ldg>

我是 Quora 的超级粉丝。我最近遇到的一个问题是关于[文件是如何从一个地方拷贝到另一个地方的。我写了一个答案，试图凭经验展示文件是如何被复制的。我认为这会是一篇很好的博文，所以我清理了原来的答案，并扩展了我含糊不清的部分，使之更加具体和正确。](https://www.quora.com/What-happens-when-a-file-is-being-copied-Does-the-computer-just-copy-the-block-file-or-does-it-copy-it-piece-by-piece-then-reconstruct-it-to-the-destination-location)

如果你不确定什么是系统调用，这篇文章可能会让你困惑。

首先，我们将创建一个大小为 1 兆字节的文件。

```
$ dd bs=1024 count=1024 if=/dev/zero of=/tmp/zeroes
1024+0 records in
1024+0 records out
1048576 bytes (1.0 MB) copied, 0.00184454 s, 568 MB/s 
```

该命令表示将 1024 个大小为 1024 字节的块从`/dev/zero`复制到`/tmp/zeroes`。所以我们要得到一个 1 兆字节(1024 * 1024)的文件，用零字节(\0)填充。

现在我们要做的是`strace`这个`cp`命令。`strace`向你展示一个程序正在进行哪些系统调用。因为要复制一个文件，你基本上必须执行一些系统调用，这应该告诉我们文件是如何被复制的。

```
$ strace -- cp /tmp/zeroes /tmp/cpzeroes

...
open("/tmp/zeroes", O_RDONLY) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=1048576, ...}) = 0
open("/tmp/cpzeroes", O_WRONLY|O_CREAT|O_EXCL, 0644) = 4
fstat(4, {st_mode=S_IFREG|0644, st_size=0, ...}) = 0
fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0
mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f35abd32000
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "", 131072) = 0
close(4) = 0
close(3) = 0
... 
```

我已经将输出精简到与我们相关的部分，因为`strace`输出了大量启动和关闭系统调用的进程，而我们对此并不感兴趣(尽管阅读并试图弄清楚发生了什么很有趣)。

让我们从最上面开始:

```
open("/tmp/zeroes", O_RDONLY) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=1048576, ...}) = 0
open("/tmp/cpzeroes", O_WRONLY|O_CREAT|O_EXCL, 0644) = 4
fstat(4, {st_mode=S_IFREG|0644, st_size=0, ...}) = 0 
```

我们打开文件`/tmp/zeroes`并取回文件描述符 3。之后，我们`fstat`了文件。`fstat`是一个获取文件状态信息的系统调用。在我的 Arch Linux 虚拟机上，我有 8.23 版本的 GNU 的 coreutils `cp`实现，所以我从这里的[下载了源代码](http://ftp.gnu.org/gnu/coreutils/)，并在内部进行了探索。[第一个 fstat](http://git.savannah.gnu.org/gitweb/?p=coreutils.git;a=blob;f=src/copy.c;h=b93c533139b6a992ce81f25c155ec0887b6240cd;hb=0f7ca66bc5d467c50c3df341f7f18bef689f8247#l1857) 用于对正在复制的文件进行错误检查，例如，确保它不是一个目录。有意思的是，`fstat`是从来不叫的。要么是`stat`要么是`lstat`，它们必须在某个时候调用`fstat`，否则我们不会在`strace`输出中看到它。

然后我们打开`/tmp/cpzeroes`，发送一些有趣的标志。`O_WRONLY`是只写，`O_CREAT`意味着如果文件不存在就创建文件，`O_EXCL`意味着如果文件已经存在，对`open`的调用将失败(我们不希望`cp`错误地丢弃我们的任何文件！).我们从`open`调用中获取文件描述符 4。

然后是另一个`fstat`电话，但我不是 100%确定为什么这个要做。为了解决这个问题，我用调试符号编译了一个版本的`cp`，并在`gdb` :
下运行

```
$ wget "http://ftp.gnu.org/gnu/coreutils/coreutils-8.23.tar.xz"
$ tar xvf coreutils-8.23.tar.xz
$ cd coreutils-8.23
$ ./configure
$ CFLAGS=-g make
$ gdb --args coreutils-8.23/src/cp /tmp/zeroes /tmp/cpzeroes
(gdb) break copy_internal
(gdb) run 
```

这将使我们停留在对`copy_internal`(我们发现第一个`fstat`的地方)的呼叫上，它看起来很有希望:

```
Breakpoint 1, copy_internal (src_name=src_name@entry=0x7fffffffeb66 "/tmp/zeroes",
    dst_name=dst_name@entry=0x7fffffffeb72 "/tmp/cpzeroes", new_dst=new_dst@entry=false, parent=parent@entry=0x0,
    ancestors=ancestors@entry=0x0, x=x@entry=0x7fffffffe6a0, command_line_arg=true,
    first_dir_created_per_command_line_arg=0x7fffffffe56f, copy_into_self=0x7fffffffe5a8, rename_succeeded=0x0)
    at src/copy.c:1746 
```

让我们确保不会错过任何打给`stat`或`lstat` :
的电话

```
(gdb) break stat
(gdb) break lstat
(gdb) continue 
```

这将在第一次`fstat`调用时首先丢弃我们:

```
Breakpoint 3, copy_internal (src_name=src_name@entry=0x7fffffffeb66 "/tmp/zeroes",
    dst_name=dst_name@entry=0x7fffffffeb72 "/tmp/cpzeroes", new_dst=new_dst@entry=false, parent=parent@entry=0x0,
    ancestors=ancestors@entry=0x0, x=x@entry=0x7fffffffe6a0, command_line_arg=true,
    first_dir_created_per_command_line_arg=0x7fffffffe56f, copy_into_self=0x7fffffffe5a8, rename_succeeded=0x0)
    at src/copy.c:1767
1767 if (XSTAT (x, src_name, &src_sb) != 0) 
```

太棒了。现在，`dst_name`上的那个在哪里？

```
(gdb) continue
Breakpoint 2, copy_internal (src_name=src_name@entry=0x7fffffffeb66 "/tmp/zeroes",
    dst_name=dst_name@entry=0x7fffffffeb72 "/tmp/cpzeroes", new_dst=new_dst@entry=false, parent=parent@entry=0x0,
    ancestors=ancestors@entry=0x0, x=x@entry=0x7fffffffe6a0, command_line_arg=true,
    first_dir_created_per_command_line_arg=0x7fffffffe56f, copy_into_self=0x7fffffffe5a8, rename_succeeded=0x0)
    at src/copy.c:1817
1817 ? stat (dst_name, &dst_sb) 
```

Tada！那就对应[这条线](http://git.savannah.gnu.org/gitweb/?p=coreutils.git;a=blob;f=src/copy.c;h=b93c533139b6a992ce81f25c155ec0887b6240cd;hb=0f7ca66bc5d467c50c3df341f7f18bef689f8247#l1907)。几行之后有一个检查，似乎是为了确保在`dst_name`没有现有的文件。所以它正在做一个`fstat`来确保它不会撞到任何东西。干净利落。

向前！

```
fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0 
```

`fadvise64`调用告诉操作系统我们打算从文件描述符 3 中顺序读取(所以较低的偏移量在较高的偏移量之前读取)。这不是必须的，但是这样做有助于操作系统优化我们的`read`调用。它将如何优化通话？嗯，它可能会获取比我们的第一个`read`所要求的更多的文件。为什么？因为从磁盘中读取较大的块要比较小的块好。为什么？要求硬盘驱动器为我们做一些事情会有很多开销。对于传统的旋转磁盘，我们必须等待读取头到达盘片上的正确位置，这可能需要几毫秒(对计算机来说是很长的时间！).因此，当阅读头处于正确的位置时，尽可能多的阅读是一件好事。如果磁盘空闲，并且我们没有进行任何 IO 操作，作为我们稍后将使用数据的一个赌注，操作系统也可能会获取更多我们正在读入内存的文件。

```
mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f35abd32000 
```

`mmap`调用可能是`malloc`的实现在我运行它的系统上处理大量分配的方式。`MAP_ANONYMOUS`标志告诉`mmap`不要通过文件来支持这种映射(因此它实质上只是变成了一个大的内存分配)。还要注意，分配的大小是 139264 字节，这将是后面要考虑的一个重要问题。

```
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
read(3, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072
write(4, "\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0"..., 131072) = 131072 
```

这实际上回答了 Quora 最初的问题。一系列 8 次读取和写入，每次大小为 131072 (128 * 1024 或 128kb)。因此，文件确实是成块复制的，而不是同时复制。

当我们用`mmap`分配的缓冲区大小是 139264 字节时，为什么读取是以 131072 的块来完成的？我最初对 Quora 答案的猜测是与 slab 分配有关(这可能不是正确的术语，但我的意思是内存分配器具有分配的“大小类”，类似于 [tcmalloc](http://jamesgolick.com/2013/5/19/how-tcmalloc-works.html) )，但这只是部分正确。为了弄清真相，我又回到了广发银行。

```
$ gdb --args coreutils-8.23/src/cp /tmp/zeroes /tmp/cpzeroes
(gdb) break copy_internal
(gdb) run
(gdb) break malloc
(gdb) continue
(gdb) bt
#0 0x00007ffff76a60d0 in malloc () from /usr/lib/libc.so.6
#1 0x0000000000410359 in xmalloc (n=n@entry=135175) at lib/xmalloc.c:41
#2 0x00000000004081b9 in copy_reg (src_sb=0x7fffffffe2c0, new_dst=<synthetic pointer>,
    omitted_permissions=<optimized out>, dst_mode=<optimized out>, x=0x7fffffffe6a0,
    dst_name=0x7fffffffeb72 "/tmp/cpzeroes", src_name=0x7fffffffeb66 "/tmp/zeroes") at src/copy.c:1156
#3 copy_internal (src_name=src_name@entry=0x7fffffffeb66 "/tmp/zeroes",
    dst_name=dst_name@entry=0x7fffffffeb72 "/tmp/cpzeroes", new_dst=<optimized out>, new_dst@entry=false,
    parent=parent@entry=0x0, ancestors=ancestors@entry=0x0, x=x@entry=0x7fffffffe6a0, command_line_arg=true,
    first_dir_created_per_command_line_arg=0x7fffffffe56f, copy_into_self=0x7fffffffe5a8, rename_succeeded=0x0)
    at src/copy.c:2523
#4 0x0000000000408fc9 in copy (src_name=src_name@entry=0x7fffffffeb66 "/tmp/zeroes",
    dst_name=dst_name@entry=0x7fffffffeb72 "/tmp/cpzeroes", nonexistent_dst=nonexistent_dst@entry=false,
    options=options@entry=0x7fffffffe6a0, copy_into_self=copy_into_self@entry=0x7fffffffe5a8,
    rename_succeeded=rename_succeeded@entry=0x0) at src/copy.c:2830
#5 0x000000000040475b in do_copy (n_files=<optimized out>, file=<optimized out>, target_directory=<optimized out>,
    target_directory@entry=0x0, no_target_directory=no_target_directory@entry=false, x=x@entry=0x7fffffffe6a0)
    at src/cp.c:767
#6 0x000000000040342b in main (argc=3, argv=0x7fffffffe898) at src/cp.c:1214 
```

我们已经得到了一个 malloc 调用，它似乎就是我们要找的那个。请看堆栈帧 2。这只是在我们深入研究任何看起来像分配的东西之前，文件名看起来与我们的兴趣相关。遗憾的是，libc 不是用调试符号编译的，所以我们必须通过查看代码来解决这个问题。

代码包含了[许多围绕缓冲区大小计算的有趣数学运算](http://git.savannah.gnu.org/gitweb/?p=coreutils.git;a=blob;f=src/copy.c;h=b93c533139b6a992ce81f25c155ec0887b6240cd;hb=0f7ca66bc5d467c50c3df341f7f18bef689f8247#l1208)。如果我看一下第 2 帧中的局部变量，我们会看到一些有趣的数字(为了可读性，输出被截断):

```
(gdb) frame 2
(gdb) info locals
buf_alignment_slop = 4103
make_holes = <optimized out>
wrote_hole_at_eof = 112
buf_size = 131072
sparse_src = false
n_read = -4294967246
buf_alloc = 0x0
source_desc = 3
name_alloc = 0x0
dest_desc = 4
dest_errno = <optimized out>
src_mode = 33188
return_val = true 
```

当对`xmalloc`(它只是一个在`malloc`失败时终止程序的`malloc`的包装器)的调用完成时，计算结果是`buf_size + buf_alignment_slop`，即 135175。我不知道`malloc`是如何在我的系统上实现的，但我知道的是这么大的分配几乎总是在页面边界上完成，所以是 4096 的倍数。4096 的下一个倍数是多少？139264.我 99%确定这就是原因。

为什么要以 131072 为单位进行读取？我猜这是硬盘的“块大小”。再说一次，这只是某种程度上的正确。关于 [`io_blksize`](http://git.savannah.gnu.org/gitweb/?p=coreutils.git;a=blob;f=src/ioblksize.h;h=1b9199525d3c97be165fd55ae3d0148a5b29f088;hb=0f7ca66bc5d467c50c3df341f7f18bef689f8247) 函数有一个可爱的评论，解释了为什么选择 128kb。

为什么在`read`和`write`调用中有大量的零字节字符串？在`mmap`的文档中，提到默认情况下，如果你提供参数`MAP_ANONYMOUS`(我们这样做了)，你得到的缓冲区被归零。这解释了第一个。其余的可以解释为我们正在复制的文件完全被零填充，并且同一个缓冲区被所有对`read`和`write`的调用重用。

```
read(3, "", 131072) = 0
close(4) = 0
close(3) = 0 
```

最后一个`read`(因为返回值小于请求的字节数，表明我们到达了文件的末尾)然后是标准的文件清理。

唷。文件拷贝中的每个系统调用都使用`cp`解释。我希望这对你和我一样有启发:)