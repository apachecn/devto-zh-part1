# gprof 和 gcov

> 原文：<https://dev.to/dzeban/gprof-and-gcov-2d59>

*gprof* 和 *gcov* 是仍在使用的经典分析器。从一开始，它们就被黑客用来在源代码级别上洞察他们的程序。

在下面的例子中，我将使用我的 [block_hasher](https://github.com/dzeban/block_hasher) 程序来说明 profilers 的用法。因为它是一个小而简单的工具。

## gprof

gprof(GNU Profiler)–简单易用的分析器，可以显示你的程序在例程中花费了多少时间，以百分比和秒为单位。 *gprof* 通过插入特殊的`mcount`函数调用来收集程序的度量，从而使用源代码插桩。

### 建筑用 gprof 仪器

为了收集配置文件，你需要用`-pg` gcc 选项编译你的程序，然后在 *gprof* 下启动。为了获得更好的结果并消除统计错误，建议多次启动主题分析。

要使用 *gprof* 进行编译，就像这样调用 gcc:

```
$ gcc <your options> -pg -g prog.c -o prog 
```

Enter fullscreen mode Exit fullscreen mode

下面是`block_hasher` :
的实际编译指令

```
$ gcc -lrt -pthread -lcrypto -pg -g block_hasher.c -o block_hasher 
```

Enter fullscreen mode Exit fullscreen mode

结果，你会得到一个插装程序。要检查它是否真的装有仪器，只需 grep `mcount`符号。

```
$ nm block_hasher | grep mcount
     U mcount@@GLIBC_2.2.5 
```

Enter fullscreen mode Exit fullscreen mode

### gprof 下剖析 block_hasher

正如我前面所说的，为了收集有用的统计数据，我们应该多次运行该程序，并积累指标。为此，我编写了简单的 bash 脚本:

```
#!/bin/bash

if [[ $# -lt 2 ]]; then echo "$0 <number of runs> <program with options...>"
    exit 1
fi RUNS=$1
shift 1

COMMAND="$@"

# Profile name is a program name (first element in args)
PROFILE_NAME="$(echo "${COMMAND}" | cut -f1 -d' ')"

for i in $(seq 1 ${RUNS}); do

    # Run profiled program
    eval "${COMMAND}"

    # Accumulate gprof statistic
    if [[ -e gmon.sum ]]; then gprof -s ${PROFILE_NAME} gmon.out gmon.sum
    else mv gmon.out gmon.sum
    fi
done

# Make final profile
gprof ${PROFILE_NAME} gmon.sum > gmon.profile 
```

Enter fullscreen mode Exit fullscreen mode

因此，每次启动都会创建 *gmon.out* ，gprof 会将其合并到 *gmon.sum* 中。最后， *gmon.sum* 将被馈送到 *gprof* 以获得平面文本配置文件和调用图。

让我们为我们的程序这样做:

```
$ ./gprofiler.sh 10 ./block_hasher -d /dev/sdd -b 1048576 -t 10 -n 1000 
```

Enter fullscreen mode Exit fullscreen mode

完成后，这个脚本将创建 gmon . profile——一个文本配置文件，我们可以对其进行分析。

### 分析

平面配置文件包含关于程序例程和在其中花费的时间的信息。

```
Flat profile:

Each sample counts as 0.01 seconds.
  % cumulative self self total
 time seconds seconds calls Ts/call Ts/call name
100.24 0.01 0.01 thread_func
  0.00 0.01 0.00 50 0.00 0.00 time_diff
  0.00 0.01 0.00 5 0.00 0.00 bdev_close
  0.00 0.01 0.00 5 0.00 0.00 bdev_open 
```

Enter fullscreen mode Exit fullscreen mode

*gprof* 指标从名字就很清楚。正如我们所看到的，我们的小程序几乎所有的时间都花在了线程函数上，**但是**看看实际的秒数——离整个程序执行只有 0.01 秒。这意味着不是线程函数变慢了，而是潜在的问题。在`block_hasher`的情况下，是一个`pread`系统调用为块设备执行 I/O。

调用图在这里真的没什么意思，就不给大家看了，抱歉。

## gcov

**gcov**(GNU 覆盖率的简称)——逐行收集函数调用统计数据的工具。通常它与 *gprof* 成对使用，以了解 slacking 函数中的哪一行限制了你的程序。

### 建筑用 gcov 仪表

正如 *gprof* 你需要用 *gcov* 标志
重新编译你的程序

```
# gcc -fprofile-arcs -ftest-coverage -lcrypto -pthread -lrt -Wall -Wextra block_hasher.c -o block_hasher 
```

Enter fullscreen mode Exit fullscreen mode

有 2 个 *gcov* 标志:`-fprofile-arcs` и `-ftest-coverage`。首先，我将对你的程序进行测试，以分析所谓的*弧*——程序控制流中的路径。第二个选项将使 gcc 为 arcs 概要分析和 *gcov* 本身收集额外的注释。

你可以简单地加上暗示了`-fprofile-arcs`和`-ftest-coverage`以及链接器`-lgcov`标志的`--coverage`选项。更多信息见 [GCC 调试选项](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html)。

### gcov 下剖析 block_hasher

现在，在检测之后，我们只需启动程序来获得两个文件——*block _ hasher . gcda*和 *block_hasher.gcno* 。请不要往里面看——我们将把它转换成文本个人资料。为此，我们执行 *gcov* 并向其传递源代码文件名。重要的是要记住你必须有`<filename>.gcda`和`<filename>.gcno`文件。

```
$ gcov block_hasher.c
File 'block_hasher.c'
Lines executed:77.69% of 121
block_hasher.c:creating 'block_hasher.c.gcov' 
```

Enter fullscreen mode Exit fullscreen mode

最后我们会得到 *block_hasher.c.gcov* 。

### 分析

文件是整个 *gcov* 工作结果。我们来看看。对于您的每个源文件，gcov 将创建带有运行时覆盖率的带注释的源代码。以下节选自`thread_func` :

```
10: 159: gap = num_threads * block_size; // Multiply here to avoid integer overflow
    -: 160:
    -: 161: // Initialize EVP and start reading
   10: 162: md = EVP_sha1();
   10: 163: mdctx = EVP_MD_CTX_create();
   10: 164: EVP_DigestInit_ex( mdctx, md, NULL );
    -: 165:
   10: 166: get_clock( &start );
10010: 167: for( i = 0; i < nblocks; i++)
    -: 168: {
10000: 169: offset = j->off + gap * i;
    -: 170:
    -: 171: // Read at offset without changing file pointer
10000: 172: err = pread( bdev->fd, buf, block_size, offset );
 9999: 173: if( err == -1 )
    -: 174: {
#####: 175: fprintf(stderr, "T%02d Failed to read at %llu\n", j->num, (unsigned long long)offset);
#####: 176: perror("pread");
#####: 177: pthread_exit(NULL);
    -: 178: }
    -: 179:
 9999: 180: bytes += err; // On success pread returns bytes read
    -: 181:
    -: 182: // Update digest
 9999: 183: EVP_DigestUpdate( mdctx, buf, block_size );
    -: 184: }
   10: 185: get_clock( &end );
   10: 186: sec_diff = time_diff( start, end );
    -: 187:
   10: 188: EVP_DigestFinal_ex( mdctx, j->digest, &j->digest_len );
   10: 189: EVP_MD_CTX_destroy(mdctx); 
```

Enter fullscreen mode Exit fullscreen mode

最左边的一列是这行代码被调用的次数。正如预期的那样，循环的*被执行了 10000 次——10 个线程每个读取 1000 个块。没什么新鲜的。*

虽然 *gcov* 对我来说不是很有用，但我想说它有一个很酷的特性——分支概率。如果你用`-b`选项
启动 *gcov*

```
[root@simplex block_hasher]# gcov -b block_hasher.c
File 'block_hasher.c'
Lines executed:77.69% of 121
Branches executed:100.00% of 66
Taken at least once:60.61% of 66
Calls executed:51.47% of 68
block_hasher.c:creating 'block_hasher.c.gcov' 
```

Enter fullscreen mode Exit fullscreen mode

你会得到很好的带有概率的分支注释。例如，下面是`time_diff`函数

```
113 function time_diff called 10 returned 100% blocks executed 100%
114 10: 106:double time_diff(struct timespec start, struct timespec end)
115 -: 107:{
116 -: 108: struct timespec diff;
117 -: 109: double sec;
118 -: 110:
119 10: 111: if ( (end.tv_nsec - start.tv_nsec) < 0 )
120 branch 0 taken 60% (fallthrough)
121 branch 1 taken 40%
122 -: 112: {
123 6: 113: diff.tv_sec = end.tv_sec - start.tv_sec - 1;
124 6: 114: diff.tv_nsec = 1000000000 + end.tv_nsec - start.tv_nsec;
125 -: 115: }
126 -: 116: else
127 -: 117: {
128 4: 118: diff.tv_sec = end.tv_sec - start.tv_sec;
129 4: 119: diff.tv_nsec = end.tv_nsec - start.tv_nsec;
130 -: 120: }
131 -: 121:
132 10: 122: sec = (double)diff.tv_nsec / 1000000000 + diff.tv_sec;
133 -: 123:
134 10: 124: return sec;
135 -: 125:} 
```

Enter fullscreen mode Exit fullscreen mode

在 60%的`if`调用中，我们使用 borrow 来计算时间差，这实际上意味着我们执行的时间超过了 1 秒。

## 结论

gprof 和 *gcov* 确实是娱乐工具，尽管很多人认为它们已经过时。一方面，这些实用程序很简单，它们实现并自动化了一个显而易见的源代码插装方法来测量函数的命中次数。

但是另一方面，这样简单的度量对于你的应用程序之外的问题没有帮助，比如内核或库，尽管有很多方法可以将它用于操作系统，比如用于 Linux 内核的。无论如何， *gprof* 和 _gcov_ 在应用程序花费大部分时间等待某个系统调用的情况下是没有用的(在我的例子中是`pread`)。

## 阅读

*   [gprof 手动](https://sourceware.org/binutils/docs/gprof/)
*   [IBM 教程](http://www.ibm.com/developerworks/ru/library/l-gnuprof/)
*   [犹他大学手册](http://www.cs.utah.edu/dept/old/texinfo/as/gprof.html)