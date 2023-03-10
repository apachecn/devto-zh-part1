# 睡眠分类:理论遭遇现实的地方

> 原文：<https://dev.to/sishaarrao/sleep-sort-where-theory-meets-sobering-reality-b3m>

大约 6 年前，一位匿名用户在 4Chan 上发帖说:

[![A screenshot of the original post on 4Chan](img/bf75b1db6a93fb81207e8eb0064fade6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NqCeHaLB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/qe9tA16.png)

他概述了这种用 Shell 编写的惊人的未被发现的类型，这种类型果然有效。[查看我的回购下载代码，自己试试！](https://github.com/SishaarRao/Sleep-Sort)

[![A screenshot of me running the code](img/f0e59766742e5b25dab8f32b7b1714e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--scPjrbmt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/y4LHcSL.png)

我承认，一开始我很困惑。我可以想象如何改进和实现这种算法，无论是减少输出值所需的时间，还是相应地扩展数据集。但是后来我对自己说,*如果这种方法(我想看起来像是 O(n)运行时)真的有效，那么为什么 IBM/Sun/Oracle 的一些天才还没有实现它呢？我不是第一个想知道如何实现这一点的人。为什么在我之前的人没有继续下去呢？为什么我们不在现实生活中使用这个呢？*

因为归根结底，电脑还是机器。归根结底，理论就是理论，现实永远是现实

## 睡眠排序是如何/为什么工作的？

睡眠排序的代码如下:

```
#!/usr/bin/env bash

sort() {
    sleep "$1"
    echo "$1"
}
while [ -n "$1" ]
do
    sort "$1" &
    shift
done
wait 
```

让我们一部分一部分地剖析这一点。(如果你想知道，`#!/usr/bin/env bash`被称为 [Shebang。](https://en.wikipedia.org/wiki/Shebang_(Unix))

#### 该功能

```
sort() {
    sleep "$1"
    echo "$1"
} 
```

这个函数会被代码反复调用。它接受它的第一个参数(用`"$1"`表示),并在这段时间内休眠。 [Sleep](https://en.wikipedia.org/wiki/Sleep_(Unix)) 是一个在给定的秒数内暂停其当前进程(或者更重要的是其线程)的命令。例如，`sleep 100`意味着进程“休眠”或暂停 100 秒。
然后在第一个参数上调用 [echo](https://en.wikipedia.org/wiki/Echo_(command)) ，打印出参数。因此，代码`sort 3`将首先休眠`3`秒，然后打印出`3`。

#### while-loop

```
while [ -n "$1" ]
do
    sort "$1" &
    shift
done
wait 
```

while-loop 的条件是`[ -n "$1" ]`正如您所看到的，我们在第一个参数中使用了`-n`标志。如果参数非零，此标志返回 true。因此，这个循环将一直重复，直到`$1`处的参数为空。

然后，我们在第一个参数`sort "$1"`上调用 sort，这是最重要的部分，然而，**是末尾的&符号(&)**末尾的&符号所做的是开始该行中描述的过程，即在不同的线程中调用 sort 函数，*。* [查看更多信息。](https://unix.stackexchange.com/questions/86247/what-does-ampersand-mean-at-the-end-of-a-shell-script-line)

在下一行，我们调用内置函数 shift。因为该函数没有参数，所以 shift 的默认值是 1。转移的作用是转移我们的论点。`$2`中的论元到`$1`,`$3`中的论元到`$2`等等。最初的第一个参数随后丢失。

#### 运行代码

[![A sample run of the Sleep Sort](img/1c16f676c99e7923adff497518c362f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hzru6uEG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/l7ixgTY.png)

正如你现在从上面的代码中看到的，我们采用第一个参数，并实例化一个单独的线程，该线程在该参数长度内休眠。一旦它醒来，它就会打印出自己的价值。1 休眠 1 秒，2 休眠 2 秒，5 休眠 5 秒，依此类推。因为较低的值睡眠较少，它们较早地打印出它们的值，因此列表现在被排序。是不是很神奇？

## 睡眠排序失败的原因

直觉告诉我，这种分类存在一些根本性的缺陷，对于大型数据集来说，它会崩溃。睡眠排序的成功依赖于 3 个基本假设，当我们超过 5 或 10 个数字时，这些假设通常不成立。

#### 1。处理器可以立即确定哪个子进程是“最少的”

概括地说，处理器固有地拥有一些存储子进程的数据结构。它可以是一棵[树](https://en.wikipedia.org/wiki/Tree_(data_structure))、一个[堆](https://en.wikipedia.org/wiki/Heap_(data_structure))，或者一些其他的数据结构，允许处理器有效地遍历它的元素并找到哪一个具有“最小”值。通过找到最小值，我们意味着找到**下一个**应该执行哪个进程。

在毫秒级上，睡眠排序可以在这里中断。如果给定值 3 和 3.00001，处理器可能会将错误的进程确定为“最小”值，原因很简单，因为无论多小，找到正确的进程都要花费处理器时间。

#### 2。计算机有多少个线程？

睡眠排序依赖于能够将每个值分离到不同的[线程](https://en.wikipedia.org/wiki/Thread_(computing))中，因此它假设**对于 n 个元素，您随后有 n 个可用的线程**。当你进入更高数量级的元素集时，这个假设就不成立了。你的计算机有一个有限的线程数量，如果你超过这个数量，那么睡眠排序将会失败。

#### 3。处理器可以在进程之间快速切换

把你的处理器想象成一个微型的心脏。当它并行执行多个任务时，实际上我们的隐喻 ticker 在我们的任务之间快速来回切换。它执行任务 A 一毫秒，然后执行任务 B 一毫秒，然后返回到任务 A。它在如此小的范围内进行切换，以至于在人眼看来，这两者似乎是并行运行的。

当你有大量的任务时，这个跑马灯就开始失灵了。正如问题#1 所指出的，它很难决定接下来要处理哪个任务，而且它也可能会被当前的任务所困扰。如果在任务之间来回切换失去了效率，睡眠排序就开始崩溃，因为任务不会按顺序完美地执行。

## 睡眠排序教会了我们什么

可伸缩性是每个软件工程师在构建系统时必须牢记的问题。通常，最好的产品是那些从一开始就考虑可扩展性的产品。

[![Scalability leads to success](img/345f4e8fcbf15beb2400476a694f3e85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dBZKHSoG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OMRk2CS.png)

睡眠排序带给我的，也是我希望它带给你的，是一个清醒的提醒:我们相信在理论世界中起作用的东西在现实世界中可能不起作用。这种说法似乎是显而易见的，但许多人创建的产品都假设了理想的情况，却没有考虑到某些假设不成立的情况。

我们需要后退一步，深入了解我们的系统是如何工作的。此外，当我们为更大或不同的数据集扩展我们的解决方案时，我们需要了解这样做时功能是如何受到影响的。获得这种洞察力的唯一方法是承认您的环境的局限性，以及您的机器的局限性。

最终，你的机器仍然是由运动的机械零件组成的。相应地设计您的系统。

我希望这篇文章有助于理解睡眠排序。如果你想亲自尝试/探索睡眠排序，请查看我的睡眠排序回购！最重要的是，如果你觉得我犯了任何事实上的错误，或者如果有任何我可以澄清/扩展/修改以改进这篇文章的地方，请评论或联系我！