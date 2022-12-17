# 用平衡三进制计算斐波那契数列

> 原文：<https://dev.to/thomasthespacefox/calculating-fibonacci-in-balanced-ternary>

# 概述

平衡三进制是一个很难理解的基数。这也很有趣，尽管这可能是个人偏见，因为我实际上是 SBTCVM 项目的首席开发人员，该项目开发平衡的三元虚拟机和相关工具以及库。主要是用 python。

虽然我不打算解释成千上万行 python 代码是 SBTCVM。让我们看一个用 SBTCVM 汇编语言编写的平衡三进制程序的实际例子(通常称为“tasm”)。(为了简单起见，这是 SBTCVM 的斐波那契演示的简化版 fib.tasm)

```
#non-important system configuration.
TTYlinedraw|on
TTYmode|54

#set initial values of CPU registers 1 and 2.
setreg1|00000000+
setreg2|00000000+

#instruction that dumps the value of register 1 to the TTY
dumpreg1|000000000|fibloopback
#add the values of register 1 and 2 and store sum into register 1
add
#store register 2 current value in scratch memory
IOwrite2|>mem1
#set register two and check for an integer overflow fault, if so leave loop
setreg2|---------
gotodataif|>fibdone
#copy register 1 to register 2
copy1to2
#read scratch memory location where reg 2 was stored.
IOread1|>mem1
#goto the "goto reference" "fibloopback"
gotodata|>fibloopback
#usual shutdown routine for VM
null|000000000|fibdone
stop 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？

# 循序渐进:

将两个起始值设置到 CPU 寄存器 1 和 2 中(1 是第一个寄存器，SBTCVM 没有“寄存器 0”)

`setreg1|00000000+`
T1】

将寄存器 1 转储到 TTY(这是输出结果)
第二个竖线后面跟着标签，是 SBTCVM 汇编中所谓的“goto reference”，基本上汇编程序计算所述指令在结果“trom”中所处的地址。
T3`dumpreg1|000000000|fibloopback`

将寄存器 1 和寄存器 2 相加，并将总和存储在寄存器 1 中(使用 9-trit 整数运算)
`add`

将寄存器 2 写入“暂存存储器 1”暂存存储器基本上只是一个充当暂存存储器的 IObus 器件。虽然内存总线标签是在源代码中定义的，但 IObus 标签是硬编码的。
`IOwrite2|>mem1`

如果算术运算“翻转”，这会将寄存器 2 设置为返回寄存器 1 的值。这始终是 9-trit 整数范围的另一端，以允许可预测的故障状态。
`setreg2|---------`

这将比较寄存器 1 和 2，如果是，则转到数据中的指令。
由于“| > label”的用法，sbtcvm 的汇编程序将其设置为“fibdone”标签的位置。
`gotodataif|>fibdone`

这会将寄存器 1 复制到寄存器 2
`copy1to2`

这将从之前读取相同的暂存存储器位置，只读取到寄存器 1 而不是寄存器 2
`IOread1|>mem1`

转到 data 中指定的地址，(同样，由于“|>label”的使用，汇编程序会自动将其设置为“fibloopback”的内存位置。
`gotodata|>fibloopback`

空操作。
`null|000000000|fibdone`

这将停止虚拟机。触发所谓的 VM SYSHALT，软停止条件。
`stop`

让我们看看输出。

```
TTY|REG1 DUMP:00000000+ 1
TTY|REG1 DUMP:00000000+ 1
TTY|REG1 DUMP:0000000+- 2
TTY|REG1 DUMP:0000000+0 3
TTY|REG1 DUMP:000000+-- 5
TTY|REG1 DUMP:000000+0- 8
TTY|REG1 DUMP:000000+++ 13
TTY|REG1 DUMP:00000+-+0 21
TTY|REG1 DUMP:00000++-+ 34
TTY|REG1 DUMP:0000+-00+ 55
TTY|REG1 DUMP:0000+0+0- 89
TTY|REG1 DUMP:000+--+00 144
TTY|REG1 DUMP:000+00-0- 233
TTY|REG1 DUMP:00+---00- 377
TTY|REG1 DUMP:00+0----+ 610
TTY|REG1 DUMP:00++0+--0 987
TTY|REG1 DUMP:0+-+--0++ 1597
TTY|REG1 DUMP:0++--0-0+ 2584
TTY|REG1 DUMP:+-0-+-0-- 4181
TTY|VM SYSHALT:
TTY|soft stop.
TTY|Press enter to exit. 
```

Enter fullscreen mode Exit fullscreen mode

可以看到，输出在 4181 处停止，因为 SBTCVM Mark 2 不能存储大于 9841 的正整数。但是 9 trits 有 19683 种组合，为什么这些数字不一样？这与平衡基数的一个基本属性有关:它们可以存储相同数量的大于零的值，就像它们可以存储小于零的值一样。这就是为什么 SBTCVM 的完整 Fibonacci 演示能够通过反转输入来轻松计算负等值。

欢迎评论和提问。