# 带锈算法

> 原文：<https://dev.to/mnivoliez/algoritmics-with-rust>

*原发于我的[博客](https://mathieu-nivoliez.com/posts/2017-04-25-algoritmics-with-rust/)T3。*

大家好，今天我们来谈谈算法！

## “算法学？它能吃吗？”

当然不是，或者仅仅是你的大脑。在谈论算法之前，我们先来谈谈算法。据[维基百科](https://en.wikipedia.org/wiki/Algorithm):

> 在[数学](https://en.wikipedia.org/wiki/Mathematics "Mathematics")和[计算机科学](https://en.wikipedia.org/wiki/Computer_science "Computer science")中，一个**算法**([![Listen](img/92c1710781718cb2128670b2f47f18de.png)](https://upload.wikimedia.org/wikipedia/commons/7/7f/En-us-algorithm.ogg "Listen")<sup>[I](https://en.wikipedia.org/wiki/File:En-us-algorithm.ogg "File:En-us-algorithm.ogg")</sup>[/lr算法可以执行](https://en.wikipedia.org/wiki/Help:IPA_for_English "Help:IPA for English")[计算](https://en.wikipedia.org/wiki/Calculation "Calculation")、[数据处理](https://en.wikipedia.org/wiki/Data_processing "Data processing")和[自动推理](https://en.wikipedia.org/wiki/Automated_reasoning "Automated reasoning")任务。算法是一种[有效的方法](https://en.wikipedia.org/wiki/Effective_method "Effective method")，可以在有限的空间和时间<sup id="cite_ref-1">[【1】](https://en.wikipedia.org/wiki/Algorithm#cite_note-1)</sup>内，用定义明确的形式语言<sup id="cite_ref-2">[【2】](https://en.wikipedia.org/wiki/Algorithm#cite_note-2)</sup>来表达，用于计算一个[函数](https://en.wikipedia.org/wiki/Function_%28mathematics%29 "Function (mathematics)")。<sup id="cite_ref-3">[【3】](https://en.wikipedia.org/wiki/Algorithm#cite_note-3)</sup>从一个初始状态和初始输入(可能是[空](https://en.wikipedia.org/wiki/Empty_string "Empty string"))，<sup id="cite_ref-4">[【4】](https://en.wikipedia.org/wiki/Algorithm#cite_note-4)</sup>指令描述了一个[计算](https://en.wikipedia.org/wiki/Computation "Computation")，当[执行](https://en.wikipedia.org/wiki/Execution_%28computing%29 "Execution (computing)")时，经过有限个定义明确的连续状态，最终产生“输出”从一个状态到下一个状态的转换不一定是确定的。一些算法，被称为[随机算法](https://en.wikipedia.org/wiki/Randomized_algorithms "Randomized algorithms")，结合了随机输入。<sup id="cite_ref-7">[【7】](https://en.wikipedia.org/wiki/Algorithm#cite_note-7)</sup>

这个挺难吃的。简单地说，算法是产生结果的一系列操作。例如，当你烤面包时，你遵循一个算法:你放面粉，然后酵母，等等...它生产面包。

又根据[维基百科](https://en.wikipedia.org/wiki/Algorithmics):

> **算法学**是[算法](https://en.wikipedia.org/wiki/Algorithm "Algorithm")的科学。它包括[算法设计](https://en.wikipedia.org/wiki/Algorithm_design "Algorithm design")，构建可以有效解决特定问题或一类问题的过程的艺术[算法复杂性理论](https://en.wikipedia.org/wiki/Algorithmic_complexity_theory "Algorithmic complexity theory")，通过研究解决问题的算法的特性来估计问题难度的研究，或者[算法分析](https://en.wikipedia.org/wiki/Algorithm_analysis "Algorithm analysis")，研究问题特性的科学，例如量化该算法解决该问题所需的时间和内存空间资源。

我们可以说算法学是研究和制造算法。这包括计算它们的复杂性或成本。

## “成本？我要付钱吗？”

是也不是。一个算法有资源成本。当你烤面包的时候，你使用配料(那些是投入)，对于每一个动作，你使用资源(像你的手，一个木勺，等等...)而那些代表成本。

我们想要的是评估这个成本，也叫复杂性。

再次与[维基百科](https://en.wikipedia.org/wiki/Analysis_of_algorithms)(是的，再次):

> 在[计算机科学](https://en.wikipedia.org/wiki/Computer_science "Computer science")中，算法的**分析是确定[执行它们](https://en.wikipedia.org/wiki/Computation "Computation")所需的时间、存储和/或其他资源的总量。大多数[算法](https://en.wikipedia.org/wiki/Algorithm "Algorithm")被设计成可以处理任意长度的输入。通常，算法的效率或运行时间被描述为将输入长度与步骤数([时间复杂度](https://en.wikipedia.org/wiki/Time_complexity "Time complexity"))或存储位置([空间复杂度](https://en.wikipedia.org/wiki/Space_complexity "Space complexity"))相关联的函数。**

## “那么，我们如何衡量呢？”

首先它存在一个特定的记法:O(f(n))(“f(n)的大 O”)其中 f 是 n 的数学函数。

请看下面的算法:

```
Entries: array a of size n
For i from 1 to n do
  print a[i] // one action here
End for
```

我们得到了一个有 n 个元素的输入数组。对于每个，我们都要打印它，所以一个动作。最后，算法应该打印 n 次。所以 n 个动作。所以复杂度是 O(n)。

不管你做一个还是两个动作，常数因子总是相对于 n。

但是在下面的算法中:

```
Entries: array a of size n
For i from 1 to n do
  For j from 1 to n do
    print a[i] // one action
  End for
End for
```

我们在第一个循环中做第二个循环。所以我们要打印 n 条消息 n 次。所以复杂度为 O(n <sup>2</sup> )。

## “好的，还有铁锈”

这只是我选择用来制作未来算法的语言。

由于这篇文章开始变大，我将在这里结束，下次见，学习冒泡排序。

马修