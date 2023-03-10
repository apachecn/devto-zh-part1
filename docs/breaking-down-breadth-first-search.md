# 分解广度优先搜索

> 原文：<https://dev.to/vaidehijoshi/breaking-down-breadth-first-search>

说到学习，通常有两种方法可以采用:你可以走得更远，尽可能地覆盖一个领域的所有方面，或者你可以走得更远，试着对你所学的主题非常非常具体。大多数优秀的学习者都知道，在某种程度上，你在生活中所学到的一切——从算法到基本生活技能——都涉及到这两种方法的某种组合。

计算机科学、问题解决和数据结构也是如此。上周，[我们深入到深度优先搜索中，了解了真正穿越二叉查找树意味着什么。既然我们已经深入了解了，那么我们有必要拓宽视野，了解其他常见的树遍历策略。](https://dev.to/vaidehijoshi/demystifying-depth-first-search)

换句话说，这是你们一直在等待的时刻:是时候打破广度优先搜索的基础了！

### DFS 和 BFS:不同，但平等

确切地说，理解什么是广度优先搜索(BFS)的最好方法之一是理解什么是*而不是*。也就是说，如果我们把 BFS 比作 DFS，我们就更容易把它们记在脑子里。所以，在我们继续深入之前，让我们重温一下深度优先搜索。

我们知道*深度优先搜索*是遍历一棵树的一个分支直到找到一片叶子，然后返回到树的“主干”的过程。换句话说，实现 DFS 意味着向下遍历二叉查找树的子树。

[![](img/22fa773e3bf71f365ec080ba48dcd4cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BKvKcWhD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVM84VPcCQe0gSy44l9S5yA.jpeg)

好的，那么广度优先搜索和它相比怎么样？嗯，如果我们仔细想想，沿着一棵树的一个分支然后另一个分支往下走的唯一真正的选择是沿着树一段一段地——或者一层一层地往下走。这正是 BFS！

**广度优先搜索**涉及一次一级地搜索一棵树。

> 在继续遍历孙节点之前，我们首先遍历整个级别的子节点。在继续遍历曾孙节点之前，我们遍历了整个孙节点级别。

好吧，这看起来很清楚。这两种不同类型的树遍历算法还有什么区别？嗯，我们已经讨论了这两种算法的不同之处。让我们思考一下我们还没有谈到的另一个重要方面:*实现*。

首先，从我们所知道的开始。上周我们是如何实现深度优先搜索的？您可能还记得，我们学习了三种不同的方法——顺序、后顺序和前顺序——使用 DFS 搜索树。然而，这三个实现是如此的相似，这真是太棒了；它们每个都可以使用*递归*来使用。我们还知道，由于 DFS 可以被写成递归函数的[，它们可以导致调用栈增长到与树中最长路径一样大。](https://dev.to/vaidehijoshi/demystifying-depth-first-search)

然而，上周我遗漏了一件事，现在提出来似乎很好(也许它甚至有点显而易见！):调用栈实际上实现了一个栈数据结构。记得那些吗？不久前我们了解了堆栈，但现在它们又出现了，到处都是！

使用堆栈实现深度优先搜索的真正有趣之处在于，当我们遍历二叉查找树的子树时，我们“检查”或“访问”的每个节点都会被添加到堆栈中。一旦我们到达一个叶节点——一个没有子节点的节点——我们就开始从栈顶弹出节点。我们再次到达根节点，然后可以继续向下遍历下一个子树。

[![](img/f44853f51dcaae02979f9bd521543b41.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xxrvwDzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYIX0wriN5SemynPCQ26LIA.jpeg)

在上面的 DFS 树示例中，您会注意到节点 *2* 、 *3* 和 *4* 都被添加到堆栈的顶部。当我们到达该子树的“末端”时，也就是说，当我们到达 *3* 和 *4* 的叶节点时，我们开始从“要访问的节点”堆栈中弹出这些节点。您可以看到右边的子树最终会发生什么:要访问的节点将被推到调用堆栈上，我们将访问它们，并系统地将它们弹出堆栈。

最终，一旦我们访问了左边和右边的子树，我们将回到根节点，没有什么需要检查的，我们的调用栈将是空的。

因此，我们应该能够使用堆栈结构，并对我们的 BFS 实现做一些类似的事情…对不对？嗯，我不知道它是否会*工作*，但是我认为至少从画出我们想要实现的算法开始，并且看看我们能走多远是有帮助的。

让我们试试:

[![](img/ec95d5bc2036b8b4387e2e27759c1c24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Obk3VZKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMaRMYq_Xk2MkNaw8lv60iA.jpeg)

好的，左边有一个图表，是我们上周实施 DFS 的结果。相反，我们如何对它使用 BFS 算法呢？

首先，我们知道要先检查根节点。这是我们最初可以访问的唯一节点，因此我们将“指向”节点 *f* 。

好了，现在我们必须检查这个根节点的子节点。

我们希望逐个检查子节点，所以让我们先检查左边的子节点——node*d*是我们现在“指向”的节点(也是我们可以访问的唯一节点)。

接下来，我们将希望转到正确的子节点。

*啊哦。*等等，根节点对我们来说已经不可用了！而且不能反向移动，因为二叉树没有反向链接！我们如何到达正确的子节点？还有……哦不，左边的子节点 *d* 和右边的子节点 *k* 根本没有链接。所以，这意味着我们不可能从一个子节点跳到另一个子节点，因为除了 node *d* 的子节点之外，我们不能访问任何东西。

哦亲爱的。我们没走多远，是吗？我们必须想出一个不同的方法来解决这个问题。我们需要想出一些实现树遍历的方法，让我们按照*的级别顺序遍历树。我们需要记住的最重要的事情是:*

 *> 我们需要保存对我们访问的每个节点的所有子节点的引用。否则，我们以后再也不能回到他们身边去看望他们了！

我想得越多，就越觉得好像我们想要保留一个包含所有我们仍然需要检查的节点的列表，不是吗？当我想保存一个东西的列表时，我的大脑会立即跳转到一个特定的数据结构:当然是队列！

让我们看看队列是否能帮助我们实现 BFS。

### 排队救援！

事实证明，深度优先搜索和广度优先搜索的主要区别是用于实现这两种非常不同的算法的数据结构。

当 DFS 使用堆栈数据结构时，BFS 依赖队列数据结构。使用队列的好处在于它解决了我们之前发现的问题:它允许我们保存对我们想要返回的节点的引用，即使我们还没有检查/访问它们。

> 我们将已发现但尚未访问的节点添加到我们的队列中，稍后再返回。

我们添加到队列中的节点的常用术语是**发现的节点**；发现的节点是我们添加到队列中的节点，我们知道它的位置，但是我们还没有实际访问它。事实上，这正是队列成为解决 BFS 问题的完美结构的原因。

[![](img/814d5d964645a1296fc5e9a8bac049fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YGLcVRRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2As2b88WiCNA2y_8dI4JxhYg.jpeg)

在左边的图中，我们首先将根节点添加到我们的队列中，因为这是我们在树中唯一可以访问的节点(至少最初是这样)。这意味着**根节点是开始**的唯一被发现的节点。

一旦我们将至少一个节点排入队列，我们就可以开始访问节点的过程，并将对它们的子节点的引用添加到我们的队列中。

好吧，这听起来可能有点混乱。没关系。我认为如果我们把它分成更简单的步骤，会更容易掌握。

对于队列中的每个节点——总是从根节点开始——我们需要做三件事:

1.  **访问**节点，这通常意味着打印出它的值。
2.  **将**节点的**左** **子节点**添加到我们的队列中。
3.  **将**节点的**右** **子节点**添加到我们的队列中。

一旦我们做了这三件事，我们就可以从队列中删除该节点，因为我们不再需要它了！我们基本上需要重复这样做，直到我们的队列为空。

好吧，让我们来看看实际情况！

在下图中，我们从根节点开始，节点 *f* 是唯一发现的节点。还记得我们的三个步骤吗？让我们现在就做:

1.  我们将访问节点 *f* 并打印出它的值。
2.  我们将对它的左子节点 node *d* 的引用进行排队。
3.  我们将对它的右子节点 node *k.* 的引用进行排队

然后，我们将从队列中移除节点 *f* ！

[![](img/b6ac8b408e7c045fc79a8d8014a5261e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NVOIdSG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbDBAJpgRhOJiVXO4zhJ0ZA.jpeg)

队列前面的下一个节点是节点 *d* 。同样，这里有三个相同的步骤:打印出它的值，添加它的左子元素，添加它的右子元素，然后从队列中删除它。

我们的队列现在有了对节点 *k* 、 *b* 和*e*的引用。如果我们系统地重复这个过程，我们会注意到我们实际上是在遍历图并按*级别顺序打印出节点。*万岁！这正是我们一开始想做的。

> 这样做的关键是队列结构的本质。队列遵循**先进先出(FIFO)原则**，这意味着首先入队的是将被读取并从队列中移除的第一个项目。

最后，当我们谈到队列的话题时，值得一提的是，BFS 算法的时空复杂度*也*与我们用来实现它的队列有关——谁知道队列会变得如此有用，对吗？

BFS 算法的时间复杂度直接取决于访问一个节点需要多少时间。由于读取一个节点的值并将其子节点入队所花费的时间不会根据节点而改变，所以我们可以说访问一个节点花费了恒定的时间，或者， *O(1)* time。因为我们在 BFS 树遍历中每个节点只访问一次，所以读取每个节点所花费的时间实际上取决于树中有多少个节点！如果我们的树有 15 个节点，它将花费我们 O(15)；但是如果我们的树有 1500 个节点，它将花费我们 O(1500)。因此，广度优先搜索算法的时间复杂度需要线性时间，或 **O(n)** ，其中**T5】nT7】是树中节点的数量。**

空间复杂性与此类似，与我们添加需要检查的节点时队列的增长和收缩程度有关。在最坏的情况下，如果树中的所有节点都是彼此的子节点，我们可能会将它们排队，这意味着我们可能会使用与树中的节点一样多的内存。如果队列的大小可以增长到树中节点的数量，则 BFS 算法的空间复杂度也是线性时间，或 **O(n)** ，其中 ***n*** 是树中节点的数量。

这很好，但你知道我现在最想做什么吗？我想实际上*写*这些算法中的一个！让我们最终将所有的理论付诸实践。

### 编码我们的第一个广度优先搜索算法

我们成功了！我们终于要写出我们的第一个 BFS 算法了。上周[我们用 DFS 算法](https://dev.to/vaidehijoshi/demystifying-depth-first-search)做了一点，所以让我们也试着写一个广度优先搜索实现。

你可能还记得我们上周用普通的 JavaScript 写了这个，所以为了保持一致性，我们将继续使用它。如果您需要快速复习一下，我们决定保持简单，将节点对象写成普通的旧 JavaScript 对象(POJO ),如下:

```
node1 = {  
 data: 1,  
 left: referenceToLeftNode,  
 right: referenceToRightNode  
}; 
```

好吧，酷。一步搞定。

但是现在我们知道了队列，并且确定我们将需要使用一个队列来实现这个算法…我们可能应该弄清楚如何在 JavaScript 中做到这一点，对吗？事实证明，在 JS 中创建一个类似队列的对象真的很容易！

我们可以使用一个数组，它很好地完成了这个任务:

```
// Create an empty queue. 
var queue = [];

// Add values to the end of the queue. 
queue.push(1); // queue is now [1] 
queue.push(2); // queue is now [1, 2]

// Remove the value at the top of the queue. 
var topOfQueueValue = queue.shift();   
console.log(topOfQueueValue) // returns 1

// The queue now has just one element in it. 
console.log(queue) // returns [2] 
```

如果我们想做得更好一点，我们也可以创建一个队列对象，它可能有像 top 或 isEmpty 这样方便的函数；但是，现在，我们将依赖非常简单的功能。

好了，就写这只小狗吧！我们将创建一个 levelOrderSearch 函数，它接受一个 rootNode 对象。

```
function levelOrderSearch(rootNode) {  
 // Check that a root node exists. 
 if (rootNode === null) {  
 return;  
 }

// Create our queue and push our root node into it. 
 var queue = [];  
 queue.push(rootNode);

// Continue searching through as queue as long as it's not empty. 
 while (queue.length > 0) {  
 // Create a reference to currentNode, at the top of the queue. 
 var currentNode = queue[0];  

 // If currentNode has a left child node, add it to the queue. 
 if (currentNode.left !== null) {  
 queue.push(currentNode.left)  
 }

 // If currentNode has a right child node, add it to the queue. 
 if (currentNode.right !== null) {  
 queue.push(currentNode.right)  
 }

// Remove the currentNode from the queue. 
 queue.shift()  
 }

// Continue looping through the queue until it's empty! 
} 
```

厉害！这其实…相当简单。或者至少比我想象的要简单得多。我们在这里所做的只是使用 while 循环来继续这三个步骤:检查一个节点、添加它的左子节点和添加它的右子节点。我们继续遍历队列数组，直到所有的内容都被删除，并且它的长度为 0。

太神奇了。我们的算法专业知识在短短一天内暴涨！我们不仅知道如何编写递归树遍历算法，而且现在还知道如何编写迭代算法。谁知道算法搜索会如此强大！

### 资源

关于广度优先搜索，以及它什么时候有用，还有很多东西需要学习。幸运的是，有大量的资源涵盖了我无法放入这篇文章的信息。看看下面几个真正好的。

1.  使用堆栈和队列的 DFS 和 BFS 算法
2.  [广度优先搜索算法](https://www.khanacademy.org/computing/computer-science/algorithms/breadth-first-search/a/the-breadth-first-search-algorithm)，可汗学院
3.  [数据结构–广度优先遍历](https://www.tutorialspoint.com/data_structures_algorithms/breadth_first_traversal.htm)，教程点
4.  [二叉树:层次顺序遍历](https://www.youtube.com/watch?v=86g8jAQug04)，mycodeschool
5.  [树的广度优先遍历](https://www.cs.bu.edu/teaching/c/tree/breadth-first/)，波士顿大学计算机科学系*