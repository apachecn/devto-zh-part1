# 到底什么是链表？[第二部分]

> 原文：<https://dev.to/vaidehijoshi/whats-a-linked-list-anyway-part-2>

链表非常简单，但是它们似乎有相当复杂的名声。正如他们所说的，他们的名声远胜于他们。

但是我对这种特定的数据结构了解得越多，我就越意识到实际上并不是*链表*令人困惑，而是决定何时以及是否使用它们的逻辑让人难以理解。

如果你从未处理过时空复杂性(或者你像我一样，有时仍然很难理解它)，一个面试问题，比如，*“请告诉我你如何将 X 实现为一个链表，以及这种实现可能的缺点是什么？”*看起来很……嗯，令人生畏的、可怕的、几乎不可能的都是想到的形容词。

所以，让我们来解构一下链表似乎总是拥有的可怕名声。我们不会被吓倒，而是将它们分解，并找出什么使它们有用，以及何时我们可能要考虑实施它们。

### 哎，那么，什么叫偶是大 O？

我们大多数人可能听说过“大 O 符号”这个术语，即使我们第一次听到有人使用它时并不知道它是什么意思。我个人对它的体验一直是在设计算法的背景下(或者被要求评估一个算法的效率)。但是在计算机科学中，大 O 真的无处不在。

原因是计算机科学——实际上，我们编写的任何东西——都是关于效率和权衡的。无论你是构建软件即服务，选择前端框架，还是仅仅试图让你的代码更简洁、更优雅，这都是我们所有人努力的目标:高效地使用我们的软件，选择对我们构建的东西重要的东西，同时意识到我们最终必须做出的权衡。

大 O 符号也是如此，但层次要低得多。**大 O 符号**是评估算法性能的一种方式。

> 当考虑算法如何执行时，有两个要点需要考虑:在给定它需要多少时间和内存的情况下，它在运行时需要多少时间。

考虑大 O 符号的一种方法是，根据我们传递给函数的元素数量，来表示函数、动作或算法运行所需的时间。

例如，如果我们有一个数字 1-10 的列表，并且我们想写一个将每个数字乘以 10 的算法，我们会考虑该算法将花费多少时间来乘以 10 个数字。但是如果不是十个数字，而是一万个呢？还是一百万？还是几千万？这正是大 O 符号所考虑的:当它的输入增长到任意大(疯狂的大！)大小。

我真的很喜欢帕克·菲尼在他那篇令人敬畏的[采访蛋糕](https://www.interviewcake.com/article/java/big-o-notation-time-and-space-complexity)博文中描述大 O 符号的用法。按照他的解释，大 O 符号是关于算法运行时增长的*方式*:

> 一些外部因素影响一个函数运行的时间:处理器的速度，计算机还在运行什么，等等。所以很难对一个算法的确切运行时间做出强有力的声明。相反，我们使用大 O 符号来表示*的运行时增长有多快*。

如果你对大 O 符号做一点研究，你会很快发现有大量不同的方程用于定义算法的空间和时间复杂性，其中大多数涉及一个 **O** (简称为“O”或有时称为“顺序”)，以及一个变量 *n* ，其中 *n* 是输入的大小(回想一下我们的十个、几千个或几百万个数字)。

然而，就链表而言，需要记住的两种大 O 方程是 **O(1)** 和 **O(n)** 。

[![](img/da789b42aabf51f8a087549283750ea9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FOYYbiVT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AFC0XX0-9Vx7yCS0dTS2Zrw.jpeg)

一个 **O(1)函数**需要*常数*时间，也就是说，不管我们有多少元素，或者我们的输入有多大:运行我们的算法总是需要同样多的时间和内存。一个 **O(n)函数**是*线性*，这意味着随着我们的输入增长(从十个数字，到一万，到一千万)，我们需要运行该算法的空间和时间线性增长。

为了稍微对比一下，我们也可以将这两个函数比作完全不同的东西:一个**O(n \)函数**,显然，你拥有的元素越多，它需要的时间和内存就越多。可以很有把握地说，我们希望避免 O(n)算法，只要看看那条疯狂的红线就知道了！

### 增长链表

我们已经知道什么链表[是由](https://dev.to/ben/whats-a-linked-list-anyway)组成的，以及它们的非连续内存分配如何使它们与它们看起来更受欢迎的表亲数组截然不同。

那么它们到底是如何工作的呢？就像数组一样，我们可以在链表中添加和删除元素。但是*与*数组不同，我们不需要预先分配内存或者复制并重新创建我们的链表，因为我们不会像预分配数组那样“耗尽空间”。

相反，我们真正需要做的是**重新排列我们的指针**。我们知道一个链表是由一个节点组成的，一个节点总是包含一些数据，最重要的是，一个指向*下一个*节点或 null 的指针。所以，我们需要做的就是在链表中添加一些东西，就是找出哪个指针需要指向哪里。

为了简单起见，我们将在这些例子中使用一个单链表。我们将从最简单的地方开始，我们可以在链表中插入一个元素:在最开始的地方。这很容易做到，因为我们不需要遍历整个列表；相反，我们只是从头开始。

1.  首先，我们找到链表的头节点。
2.  接下来，我们将创建新节点，并将其指针设置为列表的第一个节点*当前节点*。
3.  最后，我们重新安排头节点的指针指向新节点。

就这样，我们结束了！嗯，差不多了。这看起来很容易，而且只要我们按照正确的顺序完成这三个步骤，就能做到。如果我们意外地在第二步之前完成了第三步，我们会陷入一点混乱。原因是，如果我们在将新的、仍待插入的节点连接到后面的列表的其余部分之前，将头节点指向新节点*，我们将在一个只有两个节点的循环结构中结束，并且我们将有效地丢失列表中所有其他数据的*。谈论糟糕的一天！**

尽管如此，一旦你能记住这三个步骤，这个过程并不难实现(希望不要太难编码)。

在一个链表的开头插入一个元素是特别好和有效的，因为无论我们的链表有多长，它花费的时间都是一样的，也就是说它的时空复杂度是 T2 常数，或者 T4 O(1)T5。

[![](img/a32ff75c4d586a4cfe81fc5c9d5047ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rl89qhWd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJy5tjwrMdtpGl2ceq4f94A.jpeg)

但是在链表的末尾插入一个元素就是另一回事了。有趣的是，实际上*做*插入的步骤是完全一样的:

1.  找到我们想要改变指针的节点(在这个例子中，是最后一个节点)
2.  创建我们想要插入的新节点，并设置它的指针(在本例中为 null)
3.  将前面节点的指针指向我们的新节点

然而，这里有一个额外的复杂性:我们没有在列表的开头，在头节点添加一些东西。不——相反，我们在最后一个节点*后添加了一些内容。所以我们需要找到最后一个节点。这意味着我们需要遍历*整个链表*来找到它。我们知道链表是分布式的，在内存中是不连续的，这意味着每个节点在内存中的“地址”完全不同！所以遍历需要时间——节点越多，时间就越长。*

希望我们可以开始看到这种类型的插入会给我们留下什么样的时空复杂度:一个线性的。如果我们有一个 100 个节点的链表，实际上可能不会花那么长时间。即使是 1000 也可能相当快。但是想象一下，如果我们想在一个有十亿个条目的链表的末尾添加一个元素！这个插入算法将花费*和我们列表*中元素 *数量一样多的时间，根据我们的列表，这可能是我们非常糟糕的一天。*

### 列还是不列？

没有人是完美的，链表也是如此。事情是这样的:有时候，一个链表真的很棒——例如，当你想在列表的开头插入或删除一些东西时。但是，正如我们所了解的，它们有时可能…不太理想(想象一下，有一百万个节点，但只想删除最后一个！)

即使你不需要每天都和它们打交道，对链表这样的数据结构有足够的了解也是很有用的，这样当你看到它们的时候你就能认出它们，并且知道在合适的时候什么时候去接触它们。

记住链表特征的一个好的经验法则是:

> 在添加和删除大多数元素时，链表通常是有效的，但是在搜索和查找单个元素时会非常慢。

如果您发现自己不得不做一些需要大量遍历、迭代或快速索引级访问的事情，那么链表可能是您最大的敌人。在这些情况下，**数组**可能是一个更好的解决方案，因为您可以快速找到内容(分配的内存的一个块)，并且您可以使用索引快速检索列表中间或末尾的随机元素，而不必花时间遍历整个内容。

[![](img/0d7193417768f55836aedf5530bcf0c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jpHIkYNf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AcUehR5S18XSoVLaPNfNzlA.jpeg)

然而，如果您发现自己想要向列表中添加一堆元素，并且不担心以后会再次找到元素，或者如果您知道您不需要遍历整个列表，那么**链表**可能是您的新的最好的朋友。

很长一段时间，我都不知道什么是链表。当我最终真正了解它们的时候，我不知道它们究竟会被用来做什么。既然我意识到他们擅长什么，我就能看到他们的优点和缺点。如果有一天，我会知道什么时候去找他们帮我。

希望你和我一样，对链表也有同样的感觉！

### 资源

如果你认为时空复杂度和链表很傻，你可能会认为下面的链接也很傻。甚至可能是有益的！本系列的第 1 部分推荐了其中一些链接，但是您会发现它们也适用于第 2 部分。

上市快乐！

1.  大 O 记谱法初学者指南，罗伯·贝尔
2.  [大 O 小抄](http://bigocheatsheet.com/)，埃里克·罗威尔
3.  [鲁比缺失的数据结构](https://www.sitepoint.com/rubys-missing-data-structure/)，帕特·沙乌格内西
4.  [什么时候使用链表而不是数组/数组列表？](http://stackoverflow.com/questions/393556/when-to-use-a-linked-list-over-an-array-array-list)，StackOverflow
5.  数据结构:数组与链表
6.  [静态数据结构与动态数据结构](http://www.ayomaonline.com/academic/static-vs-dynamic-data-structures/)，Ayoma Gayan Wijethunga