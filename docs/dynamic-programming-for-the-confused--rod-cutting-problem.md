# 困惑的动态规划:棒切割问题

> 原文：<https://dev.to/pratikone/dynamic-programming-for-the-confused--rod-cutting-problem>

我一直纠结于优化问题。仅凭代码很难让你理解它们。因此，从我的学习中，我决定写一系列处理经典动态编程问题的文章。棒料切割问题是一个经典的优化问题，是动态规划的一个很好的例子。

### 什么问题？

杆切割问题是非常相关的任何现实世界中我们面临的问题。你有一根一定大小的鱼竿，你想把它切成几部分，然后以这样一种方式出售，这样你可以从中获得最大的收益。现在，这里有一个问题，不同大小的块的价格是不同的，切割成较小的块可能比出售较大的块获得更多的收入，所以需要不同的策略。在此之前，让我们更正式地陈述这个问题，
给你一根尺寸为 n > 0 的杆，它可以被切成任意数量的 k (k ≤ n)段。尺寸为 I 的每根杆的价格表示为 p(i)，尺寸为 I 的杆的最大收益为 r(i)(可以分成多根)。求尺寸为 n 的杆的 r(n)。

尺寸与价格表

### 思想过程

人们可以看出，问题归结为一个事实:削减将在哪里进行？
[![alt text](img/94f02cb77482fce4169230e5ed506497.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---j8y5jTu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ab7bv3tR9kUrcVtvWeXFv4A.png)

哪一个提供最大收益 r(4)？4 根尺寸为 1 的棒(通过 3 次切割获得)= 4×p(1)= 4×1 = 4
2 根尺寸为 1 的棒+ 1 根尺寸为 2 的棒(通过 2 次切割获得)= 2×p(1)+1×p(2)= 2×1+5 = 7
2 根尺寸为 2 的棒(通过 1 次切割获得)= 2×p(2) = 2 x 5 = 10
1 根尺寸为 1 的杆+ 1 根尺寸为 3 的杆(通过 2 次切割获得)= 1 x p(1) + 1 x p(3) = 1 + 8= 9
尺寸为 4 的原始杆(通过不切割获得)= 1 x p(4) = 9
因此，可能的最大收益是 10，可以通过在尺寸为 2 时切割，将原始杆分成尺寸为 2 的两根杆，并且在它们中的任何一根中都没有进一步切割来实现。
概括地说，
尺寸为 i=n 的杆的最大收益 r(n)可通过下式获得:

1.  从尺寸 i=1 开始到尺寸 i=n，重复下面的 2 和 3:
2.  决定是否必须在 I 处进行切割(导致两种不同的情况)
3.  对杆的其余部分(n-1)从 1 开始重复
4.  最后计算每种情况下的 r(n)
5.  所有计算的 r(n)中的最大值就是答案。需要额外的存储来跟踪所做的切割。递归是这方面的理想选择。在这里找到递归代码的时间复杂度是多少？这可以通过问有多少种切割方式来回答？在尺寸为 n 的杆中，可以在(n-1)C(1)个方向上进行 1 次切割 2 次切割=(n-2)C(2)k 次切割= (n-k)C(k)次切割总计:进行 1 次切割的方法+进行 2 次切割的方法+ …..进行 k 线切割的方法..+做 n-1 切的方法![alt text](img/473cb6c33ec666b5a9ca9d71bf04df1f.png)来源:[http://www . cs . UML . edu/~ kdaniels/courses/ALG _ 503 _ F12/dynamicrodcutting . pdf](http://www.cs.uml.edu/%7Ekdaniels/courses/ALG_503_F12/DynamicRodCutting.pdf)由此可见，时间复杂度在这里是指数级的。应该有更好的做事方法。一个技巧是使用带有记忆的递归，即记笔记(在这里解释)。然而，它仍然是一个指数运算，对于 n 的大值是不可行的。###用动态规划求解###问题已经显示了最优子结构和重叠子问题。r(i) =通过应用 0，1，…获得的最大收益..(i-1)分别切割成棒。r(i) = max { p(i)，p(1)+r(i-1)，p(2)+r(i-2)…。p(i-1)+r(1) }换句话说，r(i)可以通过没有切割、只有 p(i)或者在 1 处添加切割并将其添加到 r(i-1)来达到，即先前解决的杆尺寸 i-1 的最大收益问题(其可以具有多个切割)。同样，在 2 处切割，求解 r(i-2)。因此，r(i)依赖于先前计算出的小于 I 的值，但与 greedy 不同，它会对所有值进行分析以做出决定。事实上，你回去比较 I 的每一个值，这是一个重叠的子问题，每个子问题的最优值贡献给下一个子问题，因此，最优子结构。###计算### > r(1) = p(1) = 1(按大小 1 切割即不切割)> r(2) = max{ p(2)，p(1)+r(1)} = max(5，2) = 5(按大小 2 切割即不切割)> r(3) = max{p(3)，p(1)+r(2)，p(2)+r(1)} = max(8，1+5，5+1) = 8(切割

完成的表格:

完成表
最终答案为 r(5) = 13。
现在，如何找出达到 13 的切割次数？
简单，从最后一个索引即 r(5)开始回溯。
r(5)在 2 处具有切割值，这意味着在 2 处进行了一次切割。
对于剩余的 5–2 = 3 大小的部分，让我们检查 r(3)。r(3)切割值为 3，即它是作为一个整体没有切割。
因此，我们只有尺寸为 2 的裁剪。
尺寸为 5 的杆的最大收益可以通过在尺寸为 2 的位置进行切割以将其分成尺寸为 2 和 3 的两根杆来实现。
这种非递归方法是自底向上的。其时间复杂度为:

> T(n) = T(n-1) + T(n-2) +…..+T(1)+1
> T(1)= 1
> T(2)= T(1)+1 = 2
> T(3)= T(2)+T(1)+1 = 2+1+1
> T(n)= n+n-1+n-2+…以等差数列
> T(n) = O(n)

相比 O(2 <sup>n)，</sup> O(n)要好得多。
通过这种方式，人们可以发展直觉来解决最优化问题。不是每一个优化问题都可以用这种方法解决，但是它提供了一个很好的起点。

> 注意:这篇文章最初以博客的形式出现在[媒体](https://medium.com/@pratikone/dynamic-programming-for-the-confused-rod-cutting-problem-588892796840)

### 动态编程的一些资源:

[http://www . cs . UML . edu/~ kdaniels/courses/ALG _ 503 _ F12/dynamic rod cutting . pdf](http://www.cs.uml.edu/%7Ekdaniels/courses/ALG_503_F12/DynamicRodCutting.pdf)
T3】http://www . geeks forgeeks . org/dynamic-programming-set-13-cutting-a-rod/
[https://www . top coder . com/community/data-science/data-science-tutorials/dynamic-programmine-from-to-advanced/](https://www.topcoder.com/community/data-science/data-science-tutorials/dynamic-programming-from-novice-to-advanced/)