# Linux 上 C/C++的完美剖析

> 原文：<https://dev.to/etcwilde/perf---perfect-profiling-of-cc-on-linux-of>

我看到过 power-Visual Studio 用户使用分析器和调试器做出惊人的事情。看起来很神奇，但是有一个小问题。我在 Linux 上。我没有 visual studio。我现在如何侧写？也有一些针对 Linux 的分析器，每一个都有不同程度的可用性。

Perf 是我刚刚发现的一个用于分析程序的简洁的小工具。Perf 使用统计分析，它轮询程序并查看哪个函数正在工作。这不太准确，但与跟踪每个呼叫的 Callgrind 相比，它对性能的影响较小。结果仍然相当准确，即使样本较少，它也会显示哪些函数花费了大量时间，即使它错过了非常快的函数(这些函数可能不是您在分析时要寻找的函数)。

通过一个相当有创意的例子，我们以最具教育意义的方式实现了臭名昭著的斐波那契数列。基本递归。

```
// fib.c
#include <stdio.h>
#include <stdlib.h> 
int fib(int x) {
  if (x == 0) return 0;
  else if (x == 1) return 1;
  return fib(x - 1) + fib(x - 2);
}

int main(int argc, char *argv[]) {

  for (size_t i = 0; i < 45; ++i) {
    printf("%d\n", fib(i));
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

用时间运行程序给我们:

```
$ time ./fib

...

./fib  15.05s user 0.00s system 99% cpu 15.059 total 
```

Enter fullscreen mode Exit fullscreen mode

似乎程序在 40 个数字后不久就停止足够快的输出。我的机器能够在 2 秒钟内运行 40 个数字，但是如果你把它提高到 45 个，那就需要 15 秒钟。但是我太天真了，看不出这个问题。我需要侧写。

```
$ perf record ./fib 
```

Enter fullscreen mode Exit fullscreen mode

使用这个命令再次运行程序会生成`perf.data`文件，它包含了我们程序的所有时间信息。除了创建这个新文件，没有什么令人兴奋的东西出现。它显示了 perf 唤醒的次数、样本数和 perf.data 文件的大小。

```
[ perf record: Woken up 10 times to write data ]
[ perf record: Captured and wrote 2.336 MB perf.data (60690 samples) ] 
```

Enter fullscreen mode Exit fullscreen mode

当我们奔跑时，事情变得有趣起来:

```
perf report 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们以下的观点

[![perf default output](img/5520f308e6b7de7dff49774f180dad78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fObqZ3P_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d5lqrr3dazj4rue5bdxd.png)

好极了。所以基本上，它没有告诉我们太多。它确实说我们把所有的时间都花在了 fib 函数上，但其他的就不多了。我们可以在突出显示`fib`功能的情况下点击回车键，得到几个选项。一个是注释函数。这向我们展示了函数中指令的反汇编，以及每个指令所用的时间百分比。因此...我想我们可以在装配层面进行优化？似乎有点偏激。那对我们大多数凡人来说，有点太低级了。

[![perf disassembly](img/f869f078d68fd8cb84ccf31c40333a14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CtyemvuU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0jt1ygl6o0nzdgvqgf53.png)

如果我们够聪明的话，这个视图中实际上有一些有用的信息。我们稍后再讨论这个问题。

默认情况下，perf 仅收集时间信息。我们可能想看通话记录，因为我们在打电话。也许基例才是罪魁祸首(返回一个单号毕竟总是可疑的(这里跟我一起笑，那是个笑话))。

为了得到调用图，我们将`-g`选项传递给`perf record`。

```
$ perf record -g ./fib 
```

Enter fullscreen mode Exit fullscreen mode

并等待它再次运行...然后再次运行`perf report`查看新视图。

[![perf graph](img/8ae23f2018a7bdaf1af81bb670cc1020.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s5vsmI2X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxx3yg7wt8ex5lafzpll.png)

不是超级不一样。整洁！好的，第一次和第二次有一些不同。也就是说，它现在有一个`children`和`self`列，以及一些函数调用旁边的一些`+`符号，而不是有一个`overhead`列。

Children 是指后续调用的开销，self 是指函数本身的开销。高的子开销和低的 self 开销表明该函数进行的调用开销很大，但是高的 self 开销表明该函数本身开销很大。

`+`表示该函数进行了可访问的调用。并非所有的函数都是可跟踪的，可能是因为优化器剥离了函数名(或者是剥离了调试符号的`strip -d`)来收缩二进制文件。

突出显示一行并按 enter 键将展开它，显示调用的函数。注意，一些旧版本的`perf`默认显示被调用者，而不是我们正在查看的函数所调用的函数。如果您使用的是旧版本，可能需要通过使用`perf record -g 'graph,0.5,caller'`调用 record 来改变这种行为。

[![perf graph expanded](img/ae16418844af8ff0f3dc57f257628f56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yh9g5Dk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/858b8ev0c7rlviowp8y7.png)

在我的例子中，我扩展了对`main`函数的调用，它本身并不花费太多时间。在内部，有一堆对 fib 函数的调用。最下面一个旁边有一个`+`符号，表示可以展开。展开它显示了对 fib 的另一个调用，带有一个`+`符号。

因此..最终，我们得到了一个又一个长长的调用链。所以递归又来了！
回到反汇编，大部分时间花在做`mov %rsp,%rbp`上，这些寄存器负责 x86 架构中的堆栈和帧指针。因为我们看到有一个对 fib 函数的调用链，并且这个特定的指令非常热门，所以有一个很好的迹象表明递归的某些东西是罪魁祸首。现在我们知道该修复什么了。修复它取决于你。一些解决方案可能包括内存化，或者用堆栈和 while 循环模拟递归，或者两者的结合，但这超出了本文的范围。

虽然这是一个虚构的示例，但它介绍了如何使用 perf 工具进行分析。还有许多其他特性没有在这里介绍，但是这应该足以让您开始使用 perf 进行分析。perf 的结果并不完美，但它们是有用的。还有其他工具，但我更喜欢 perf 的接口，它不会对运行时性能造成很大影响。你怎么想呢?你最喜欢的剖析器和剖析工具是什么？