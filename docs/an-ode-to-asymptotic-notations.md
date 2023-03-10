# 渐近记数法颂歌

> 原文：<https://dev.to/smellycode/an-ode-to-asymptotic-notations>

在算法的乐土上，有很多方法可以描述算法的效率和可伸缩性，尤其是当输入量很大的时候。总之，这些方法被称为渐近符号([渐近分析](https://en.wikipedia.org/wiki/Asymptotic_analysis))。在这篇文章中，我们将熟悉这些符号，并看看如何利用它们来分析算法。

> 注意:我写这篇文章是为了学习，因为我坚信通过教学来学习。如果您有任何建议/修正，请在评论区发表。

在我们深入分析算法之前，我们需要知道什么是算法，以及为什么学习/分析算法是重要的。所以让我们开始吧。

### 什么是算法？

根据 [Merraim Webster](https://www.merriamwebster.com/dictionary/algorithm) ，

> **n 算法是一种在有限的步骤中解决数学问题(如寻找最大的[公约数](https://www.merriam-webster.com/dictionary/common%20divisor))的程序，它经常涉及重复运算；*广义上的*:解决问题或完成某些目标的一步一步的过程，尤指通过计算机。**

 **换句话说，**一个算法仅仅是一组有限的定义明确的指令，用来解决一个特定的问题** ( [Ted-Ed:什么是算法？](https://www.youtube.com/watch?v=6hfOvs8pY1k))。例如，我们想找到一个数组中的元素。找到它的一种方法是，遍历数组中的每个元素，并与关键元素进行比较。如果相等，那么我们说这个元素存在于数组中，否则我们继续遍历直到数组的末尾(线性搜索)。

[![](img/e623a4cbd371b63c204dc7cd58a96e6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XT0gGxih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Am6Ni_Ry4iRIPMHY44uy49A.gif)

一般来说，算法是用伪代码写的。*伪代码就是用非正式语言编写的解释算法的文本，可以翻译成任何编程语言*。把伪代码想象成一个基于文本的算法设计工具。让我们为上面的例子写伪代码。

```
Find Element(array, key)
a) Set i = 1;
b) if i is greater than array_size then go to step g. 
c) if array[i] is equal to key then go to step f.
d) set i = i + 1;(increase i by 1).
e) go to step b.
f) print 'Present' and go to step h.
g) print 'Not present'.
h) Exit.

findElement(array, key)
    for each item in array
        if item == key
            return 'Present'.
    return 'Not present'. 
```

如果你一个人住，这里有另一个算法可能对你有帮助。

[![](img/9783dabc48a3abecce52037cda3aa2cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mJtQLRT0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A7fNjcSRx41hk5gtZCtLtzg.png)

根据科尔曼[对算法](https://en.wikipedia.org/wiki/Introduction_to_Algorithms)的介绍，

> 对于每个输入实例，如果一个 n 算法以正确的输出停止，那么这个算法就是正确的。

我们向算法提供各种输入实例(包括边缘情况)来衡量其正确性。例如，如果我们传递一个空数组作为输入，线性搜索算法将如何表现。

> 算法本质上是明确的。

这意味着对于一个算法来说，它的每一步&输入/输出必须是清楚的，并且只能导致一个意思。

### 为什么要学算法？

作为工程师，我们总是努力提供更好的解决方案。我们知道解决一个问题通常有很多方法，每种方法都有自己的优点和缺点。研究解决方案/算法有助于我们决定选择哪个解决方案。例如，如果我们知道输入数组是有序的，我们可以使用二分搜索法来代替线性搜索来查找数组中的元素。在研究算法时，我们考虑以下问题:

1.  它解决了所有可能输入的问题吗？
2.  它有效地利用资源吗？
3.  可以优化吗？
4.  是否可扩展？
5.  有没有更好的算法？
6.  这样的例子不胜枚举…

* * *

算法的执行需要许多资源，如计算时间、内存、通信带宽、计算机硬件等。当我们分析一个算法时，我们主要关注算法所需的计算/运行时间和内存/空间(又名**时间复杂度** & **空间复杂度**)。让我们确定我们的线性搜索算法的时间复杂度。这是一个简单的用于 LinearSearch 的 JavaScript 程序。

```
/**
 * Linear Search.
 * @param array
 * @param element 
 */
const linearSearch = (array, element) => {

  // Traverse elements of the array and compare 
  // them
  for (let i = 0; i < array.length; i++) {

    // Compare element at ith position with 
    // key element. If both are equal then return 
    // the index.
    if (array[i] === element) {
      return i;
    }

  }
  // no element found. Returns -1.
  return -1;
}; 
```

你可以看到我们的 linearSearch 函数有两个参数:`array`和`element`我们希望找到。它只是使用`for`循环遍历`array`，并将数组的每一项与元素参数进行比较。让我们假设比较语句需要时间`a`计算机时间的单位，那么数组的大小是`n`

1.  **最佳情况**:数组中第一个条目等于元素。只有一次迭代，所以`time = a`；(找到元素所需的最短时间)。
2.  **最坏情况**:数组中的最后一个条目等于数组中不存在的元素或元素。`n`迭代，所以`time = a * n`；(查找元素所需的最长时间)。
3.  **平均大小写**:第一个和最后一个匹配元素之间的任何条目。因此所需时间将介于最小和最大时间(`a â‰¤ time â‰¤ a * n`)之间。

通过观察上述场景，我们可以得出结论:算法的运行时间取决于输入的大小以及计算机运行算法代码行所花费的时间。换句话说，我们可以把运行时间看作是其输入大小的函数**。对于线性搜索，它是 F(n) = an。**

 **我们考虑分析的另一个重要因素是函数的增长率。增长率(也称为增长顺序)描述了当输入增加时运行时间是如何增加的。这是线性搜索相对于输入大小的运行时间图。

[![](img/b4ec1f8814f2cc554f044f6c9614ecfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qgdrzo10--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A0uv2onCJce62g9j4Cb0_bQ.png)

当我们谈论增长率时，我们主要关注函数中具有高阻抗并导致算法运行时间剧烈变化的部分，尤其是当输入量非常大时。

[![](img/45e77e985f2fde1569a8cce0335ff35c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i2SLr4CS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2A0t6SK413e7UhK_-lumfcBA.gif)

考虑一个二次函数 F(n)= anβ+bn + c，对于非常大的 n 值,( bn+c)项变得不太重要，将其添加到 anβ几乎没有任何影响。这就像把一个装满水的桶扔进大海或者在ðÿ˜€.大秀上所以我们可以停下来。现在我们只剩下一个。正如我们所知,“a”是常数系数，我们希望将全部注意力放在输入的大小上，这样我们也可以去掉“a”。因此，我们只剩下了 n，这才是对我们真正重要的。它告诉我们函数的运行时间增长为 n，这意味着它的增长率是二次的。同样，我们可以说线性搜索的增长率是线性的(n)。

*当我们移除不太重要的项和系数时，我们得到了函数的增长率，但我们需要更正式/通用的方式来描述它们，这时我们使用渐近符号*。

> 把渐近符号看作是谈论函数和对它们进行分类的一种正式方式。

渐近符号有以下用于描述
函数的类别或符号:

1.  *O*-批注(大哦)。
2.  ‖-记数法(大ω)。
3.  *ì*-记数法(Theta)。
4.  *o*-批注(小哦)。
5.  “ω”符号(小ω)。

在本帖中，我们将讨论前三个符号，即 *O* 、*、*和*。如果你想了解另外两个符号，你可以在 [CLRS](https://en.wikipedia.org/wiki/Introduction_to_Algorithms) 中阅读。所以我们就一个一个来讨论吧。*

 *#### *O 符号:*

在[通俗英语](https://stackoverflow.com/questions/487258/what-is-a-plain-english-explanation-of-big-o-notation?rq=1)中，大 oh 符号是*在输入很大的情况下*估计函数上界/极限的一种方式。让我们把它写成数学形式。

```
O(g(n)) = { 
            f(n): there exist positive constants c and 
                  k such that 0 â‰¤ f(n) â‰¤ cg(n) for 
                  all n â‰¥ k
          } 
```

如果你不能理解它，不要担心。我与你同在，“愿原力与我们同在”。让我给你分析一下。首先，我们选择了一个称为 k 的任意常数作为大输入的基准，这意味着只有当 n \u k 时我们的输入才被认为是大的。之后，我们在函数 g(n)和常数 c 的帮助下定义函数 f(n)的上界。这意味着我们的函数 f(n)的值将始终小于或等于 c * g(n ),其中 g(n)可以是所有足够大的 n 的任何非负函数。例如， 对于线性搜索，g(n) = n。因此，对于最坏情况和平均情况，线性搜索的时间复杂度都是 O(n)，即 n 的阶。对于最佳情况，线性搜索的时间复杂度是 O(1)，即恒定时间(关键元素是数组的第一项)。

[![](img/0f25d81bf5faaffbb4e1a6eccf33cee6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9EwrZPyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AzrzHZKPVEtoaCY5PaYp4gg.png)

大 Oh 符号是时间复杂度最广泛使用的符号，因为它讨论了算法的上限，这有助于我们决定哪种算法更好。例如，对于足够大的输入，复杂度为 O(n)的算法将比复杂度为 O(n)的算法做得更好(当输入很小时，这可能不成立)。

#### 评分:

欧米伽符号是大 Oh 符号的逆符号。这是*一种当输入非常大*时估计函数下限/极限的方法。

```
Î© (g(n)) = {
             f(n): there exist positive constants c and
                   k such that 0 â‰¤ cg(n) â‰¤ f(n) for
                   all n â‰¥ k
           } 
```

正如我们所见，函数值总是大于或等于下界，即 c * g(n)。

[![](img/4ade717d007c52666f3bba32648ea4f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sf0J-Sij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXp0SJdXNrh8b7E11xMn_IQ.png)

当我们对函数/算法所需的最少时间感兴趣时，Omega 符号非常有用，这使得它成为其他符号中使用最少的符号。

#### *——批注:*

 *θ符号是渐近紧界的，这意味着我们的函数位于上界和下界之间。

```
Î˜(g(n)) = {
             f(n): there exist positive constants a, band k such 
             that 0 â‰¤ b * g(n) â‰¤ f(n) â‰¤ a * g(n) for all n â‰¥ k
          } 
```

[![](img/2546e70f720ca8351bdd20afc862b2f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0wxwXylm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_sgf8iyO4A0UaupHcGTNpQ.png)

正如我们所看到的，如果 f(n)是 g(n)的θ，那么对于大于或等于 k 的所有 n 值，f(n)总是位于 b * g(n)和 a * g(n)之间。例如，我们知道插入排序的上界是 n 的阶(当数组以逆序排序时，最坏的情况)，下界是 n 的阶(当数组已经排序时，最好的情况)。因此，在符号中，我们使用以下语句描述插入排序的时间复杂度:

1.  插入排序的最坏情况时间复杂度是(n)。
2.  插入排序的最佳时间复杂度是(n)。
3.  插入排序的平均事例时间复杂度是(n)。

### 使用哪种记数法？

经常有人问*我们应该用哪种符号来描述算法的复杂性*？嗯，这要看情况和想要的说法。让我们举一个插入排序的例子。我们知道，对于插入排序，最坏情况下的时间复杂度是(n ),最好情况下的时间复杂度是(n)。我们可以说插入排序的时间复杂度是(n)吗？不，我们不能，因为我们知道最好的情况是。同样，我们不能说它是(n ),因为最坏的情况是(n)。一个敏锐的读者可能会问，有没有办法用一个单一的/总括的陈述来涵盖这两种情况，并告诉我们复杂性，而不是谈论最好的情况和最坏的情况？是的。有。如果代替符号，我们使用大 O 符号，那么我们可以说插入排序的时间复杂度是 n 的阶，即 O(n)那么我们不需要担心线性时间，即 O(n)导致上界 n 覆盖它。关于使用哪种符号的详细讨论可以在这个 [StackExchange 线程](https://cs.stackexchange.com/questions/57/how-does-one-know-which-notation-of-time-complexity-analysis-to-use)中找到。

### 总结

总而言之，我们可以说渐近符号是谈论算法的奇特方式/类别。它们帮助我们比较不同的算法。它们还帮助我们在各种情况下推理我们的算法，如最佳情况、最差情况和一般情况。

### 引用:

1.  由[CLRS([https://en.wikipedia.org/wiki/Introduction_to_Algorithms](https://en.wikipedia.org/wiki/Introduction_to_Algorithms))介绍算法。
2.  渐近符号由 [GeeksForGeeks](http://www.geeksforgeeks.org/fundamentals-of-algorithms/#AnalysisofAlgorithms) 提供。
3.  [可汗学院](https://www.khanacademy.org/computing/computer-science/algorithms/asymptotic-notation/a/asymptotic-notation)。
4.  Stackoverflow.******