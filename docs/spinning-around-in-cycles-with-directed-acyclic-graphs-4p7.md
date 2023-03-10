# 有向无环图的循环旋转

> 原文：<https://dev.to/vaidehijoshi/spinning-around-in-cycles-with-directed-acyclic-graphs-4p7>

在本系列的整个过程中，我写了很多关于重新发现计算机科学中我以前听说过或读到过的东西，但没有足够的上下文来理解。

起初，我认为这种一次又一次偶然发现相同概念的奇怪循环过程是计算机科学独有的。但是最近，我有了不同的看法:学习和再学习一个想法的过程是更深层次理解它的基础。这个想法有一个名字:[解释学圈](https://en.wikipedia.org/wiki/Hermeneutic_circle)。在它的核心，解释学循环是基于这样一种理念，即我们必须一次又一次地回到观念，并且一次理解它们的一部分，以便完全理解整体(反之亦然)。

这种循环过程正是我在学习不同类型的图时所经历的，包括有向图、循环图以及介于两者之间的一切。我以前听说过这些类型的图表，对它们都略知一二，但并不从根本上理解它们何时会被使用，或者为什么它们会在我阅读的所有资料中一次又一次地出现。

事实证明，学习很难！这需要时间，也需要大量的重复。所以，让我们回到图，了解它们的一个特定子集，这些子集是计算机科学中许多解决方案的基础。

### 边缘，棱角，无处不在

在我们探索图形的过程中，我们主要关注于代表图形的[和](https://dev.to/vaidehijoshi/from-theory-to-practice-representing-graphs)[如何通过它们搜索](https://dev.to/vaidehijoshi/going-broad-in-a-graph-bfs-traversal)。上周，我们看了 ***深度优先搜索(DFS)*** ，这是一种图遍历算法，[递归地确定](https://dev.to/vaidehijoshi/deep-dive-through-a-graph-dfs-traversal)两个给定节点之间是否存在路径。然而，由于一些原因，再次回到深度优先搜索是值得的。

<figure>[![](img/e25c3e0a10c8ca9082119a63a3c347c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aVjReMyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwFHq_9nahZkd_W33Ze3A1Q.jpeg)

<figcaption>DFS 算法有助于识别循环、确定边和顶点排序。</figcaption>

</figure>

深度优先搜索有助于我们了解更多关于给定图形的信息，并且在对图形中的节点进行排序时特别方便。我们可以使用 DFS 算法来识别图中的圈，确定图的边的种类，并以线性方式对图中的顶点进行排序。

今天我们将逐一讲解这三个技巧，看看使用深度优先搜索如何帮助我们更好地理解在搜索*到* it 的过程中我们正在处理哪种类型的图表！

在本系列的早期，我们[学习了边](https://dev.to/vaidehijoshi/a-gentle-introduction-to-graph-theory)，即连接图中节点的元素。我们还了解到边可以是*定向的*，从一个节点流向另一个节点，或者是*非定向的*，在两个顶点之间双向流动。但是，保持这篇文章的主题，让我们回到我们已经知道的事情上来。

如果我们回到边，我们会看到，在图的上下文中，边不仅仅是“有向”或“无向”的。两条有向边可能会非常不同，这取决于它们在图中出现的位置！让我们来看看我们可以对图中的边进行分类的其他一些方法。

<figure>[![](img/2cfdba6e8b6bae1747454683f39693eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J3k6kCnu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Al8JVWt_eoZzcAUjgJECeDw.jpeg) 

<figcaption>图中边缘分类，partβ1</figcaption>

</figure>

在图形数据结构中有两种主要的边:一种**树边**和一种**非树边**。我们还记得，在[的 BFS 算法](https://dev.to/vaidehijoshi/going-broad-in-a-graph-bfs-traversal)和[的 DFS 算法](https://dev.to/vaidehijoshi/deep-dive-through-a-graph-dfs-traversal)中，指向起始节点的*父指针*可以重新排列以形成一棵树。

作为图遍历算法中所采用的路径的一部分的任何边被称为 ***树边*** ，因为它是允许我们每次遍历图时访问新顶点的边，并且可以被重构以形成“树”。

那么，非树边呢？嗯，有三种不同的变体，我们实际上已经遇到了所有的变体，即使我们当时不一定知道它们叫什么。

三条边中的第一条被称为*，因为它允许我们在图中“向前”移动，并且可能是树中另一条路径的一部分。相比之下，还有 ***向后边*** ，它将图中的一个节点“备份”到它的一个祖先(例如，它的父节点或祖父节点，甚至回到它自己！).最后是 ***交叉边*** ，它“交叉”并将图中的一个子树连接到另一个子树。换句话说，交叉边连接到不一定在树路径中共享祖先的兄弟节点，但是无论如何连接它们。*

 *当我们看到这些定义被应用到图中的实际节点时，它们就更有意义了，所以让我们来看一个例子。

<figure>[![](img/7ec91cb288550c1155d2c1a3d426c26f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VTk_76Yb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AX01zera2nJmsk2Z_6Y4wtA.jpeg) 

<figcaption>图中边缘分类，part 2</figcaption>

</figure>

在这里显示的插图中，我们可以看到树的边缘，用蓝色突出显示，是我们遍历图中路径的一部分；在这种情况下，我们的行走是 u-v-y-x。在我们的路径中连接这些顶点的边是我们的*树边*。然而，我们也可以在不同的道路上“向前”走，从 u 走到 x；因此，边缘(u，x)是一个*前沿*。

我们会注意到，当我们到达节点 x 时，唯一要导航到下一个节点的顶点是 v。连接树中这两个节点(x，v)的边是一条*后向边*，因为它将节点 x 连接回路径中它的祖先之一:节点 v。我们还会注意到，在图的另一部分——在不同的子树中，而不是——节点 z 有一条后向边，它实际上连接着【T2 这也被称为 ***自循环*** ，或者将节点连接回自身的向后边，或者“引用”它来自的节点的边:(z，z)。(我们稍后将回到自循环，但这是我们对它们的第一次体验！)

连接节点 w 和 y 的边，我们也可以称之为(w，y)，是一条*交叉边*，因为它连接子树。当我们重新排列我们的树的边，实际上*形成*一棵树时，交叉边更容易看到，所以让我们重新绘制相同的图形，使我们的交叉边更明显。在下图中，我们将看到两个潜在的“树”，它们可以从图中的两个不同节点开始形成:分别是节点 u 或节点 w。

<figure>[![](img/76a2771b41cd9830da68f1d3534082ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w6f31-ra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2fi-xG12CyAQ8Ih4CzP76Q.jpeg) 

<figcaption>一个图形，被重新想象为连通(子)树</figcaption>

</figure>

边(w，y)将整个图的两个子树连接在一起，即使子树实际上没有单个“根节点”或祖先。请记住，图是树的一个非常“松散”的版本，并且[不遵循树所遵循的规则](https://dev.to/vaidehijoshi/a-gentle-introduction-to-graph-theory)。

> 因为一个图没有一个单独的根节点，所以它不能形成一个单独的树。更确切地说，一个图可以包含许多小的子树，当我们走过图的路径时，每个子树都会变得很明显。一个图与其说是一个单独的树数据结构，不如说是一个相互连接的森林，其中有小的子树。

关于所有这些不同种类的边，有一点需要注意:它们并不总是存在的！这完全取决于我们处理的是什么类型的图。在*有向*图中，树边、交叉边、后向边和前向边都是可能的。然而，对于一个*无向图*，这是一个完全不同的故事。

<figure>[![](img/f2f716806aaec85201b8ca047aac74d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AoYQuKUK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXr7NbsTsh5bQFVG9kvKvUw.jpeg) 

<figcaption>一个无向图只能有树边或向后边，部分 1</figcaption>

</figure>

在无向图中，没有前向边或交叉边。每条边都必须是树边或后边。

> 记住边分类规则的一个简单方法是:无向图只能有树边和后边，但是有向图可以包含所有四种边类型。

好吧，但是这个规则背后的故事是什么？好吧，如果我们真的在一个例子上尝试，会更容易看出来，所以我们就这么做。

<figure>[![](img/e56bb417f1440965f6263b57d2a74d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6K82y-N5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATbPs52d3masVAkbp6uTPpQ.jpeg) 

<figcaption>一个无向图只能有树边或向后边，部分 2</figcaption>

</figure>

在这里显示的图表中，我们有四个顶点和四条边。假设边(a，d)是一个“向前”的边；换句话说，它是一条边，允许我们从节点 a 移动到节点 d。

想象一下，我们穿过这个图，从节点 a 到 b，然后从 b 到 c。当我们到达节点 c 时，我们没有其他地方可去，所以我们必须回溯到节点 b。

现在，为了让我们正确地运行深度优先搜索算法，我们需要检查是否有任何其他我们可以从节点 b“访问”的顶点。我们将访问节点 d。我们再次处于类似的情况，我们必须检查是否有另一个节点可以从 d 访问。事实证明，由于这是一个无向图，所以有一个地方我们可以访问:节点 a。此时，边(a，d)不可能是“前向”边。因为 DFS 的规则规定我们*必须*从 d 访问节点 a，因为我们必须在*最深的*级别上探索图，我们必须访问它；我们不能回溯*向上*到节点 a，然后从那里访问 d！

一个无向图永远不会允许像(a，d)这样的前向边，因为为了使前向边存在，我们需要在遍历该边之前，完全访问完从起始节点 a 可到达的所有节点。在无向图中，这实际上是不可能做到的！因此，这两个节点之间的边实际上应该这样写:{a，d}，因为我们可以在这条边上的任何方向上遍历，这取决于我们从哪个节点开始。

尽管我们在这里不看它的例子，我们可以否定类似于正向边的交叉边，因为它们也不能存在于无向图中。

### 团团圆圆

深度优先搜索算法的另一个优点是它能够识别图中的循环。在我们深入研究如何做到这一点之前，让我们先熟悉一些我们最终会用到的重要术语。

一个 ***循环*** ，在一个图的上下文中，当一些顶点在一个封闭的边链中相互连接时发生。包含至少一个循环的图称为 ***循环图*** 。相反，包含零个循环的图称为 ***非循环图*** 。

<figure>[![](img/4f16fd8baec20bfbf46353a81aede3a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lUDUOX8P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGnMghFkLYP6LGbbGsPYLww.jpeg) 

<figcaption>循环与非循环曲线图</figcaption>

</figure>

在上一节中，我们看到了第一个 ***自循环*** 实例，这是一种后向边缘。自循环只能出现在*有向*图中，因为自循环是一种*有向*边。

<figure>[![](img/bc62cadc58c5c07b3d7b1735586eed47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7oPOnaPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyH6b4iPJI1RTDtWJSbpXLw.jpeg) 

<figcaption>一个有向循环图，带有一个自循环</figcaption>

</figure>

有向图和无向图都可以包含循环，但值得注意的是，自循环只能出现在 ***有向循环图*** 中，这是一个至少包含一个循环的有向图。

> 事实证明，深度优先搜索算法特别擅长检测循环的原因是因为它在查找后向边缘方面很有效。

根据定义，任何有后向边的图都包含一个圈。在下图中，我们可以看到节点 e 连接回了路径中的祖先节点 a。因为在这个有向图中存在向后的边，所以我们知道这个图也包含一个循环——它是一个*有向循环图*。

<figure>[![](img/46eb983924567c042441bbfbba6fa502.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rzwyRvTY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQvIWlS6sc9fKOexXFqH_fQ.jpeg) 

<figcaption>循环检测和反向边沿</figcaption>

</figure>

好吧，但是 DFS 实际上是如何检测周期的呢？为什么这个算法在确定一个图中是否存在循环时特别有效？嗯，这一切都要追溯到深度优先搜索算法的递归性质。我们将回忆起 DFS 使用栈数据结构[的幕后](https://dev.to/vaidehijoshi/deep-dive-through-a-graph-dfs-traversal)。这使得我们在遍历图形时，知道路径中是否存在后向边变得稍微容易一些。

<figure>[![](img/36a1f912fc70d7f6b994300f0ac389de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M-GsELtp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJXgI4Jg2pqcGcaB51-X5_Q.jpeg) 

<figcaption>我们可以使用 DFS 来检测 a 图中的循环。</figcaption>

</figure>

例如，在前面的示例图中，当我们从 u 到 v 到 y 到 x 遍历时，我们将这些元素添加到我们访问过的节点堆栈中。我们可以采取一个额外的步骤来简化边的分类:当我们开始搜索它的“最深”路径时，我们可以用一个简单的布尔标志将节点 u 标记为当前正在处理。一旦我们将节点 u 标记为我们正在搜索的节点，如果我们添加到引用*的堆栈中的任何节点备份*到 u，我们知道我们已经找到了一个循环。事实上，同样的逻辑适用于图中的其他内部循环，而不仅仅是连接回起始节点的循环。

> 如果添加到堆栈中的任何节点都引用了已经在堆栈的中的*节点，我们可以确定在这个图中有一个循环。*

但是等等——还有更多！嗯，好的——还有一个。我们需要定义的最后一种图是没有圈的有向图*。*

深度优先搜索的大多数实现都会检查是否存在任何循环，其中很大一部分是基于 DFS 算法检查一个图是否是一个 ***有向无环图*** ，也称为 ***DAG*** 。有向无环图，顾名思义，是有向的，但是没有任何环。有向无环图的黄金法则是，如果我们从图中的任何一个节点开始，没有边序列允许我们返回到起始节点。因此，根据定义，有向非循环图永远不会包含自循环。

<figure>[![](img/e32f0cbc72f5e6d6e820bc6618bd217b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M-ZcnmB9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbtUG3yUhEPfj5_ESgE4I9w.jpeg) 

<figcaption>【有向无环图】</figcaption>

</figure>

Dag 在计算机科学中有些声名狼藉，因为它们在软件中几乎无处不在。例如，有向非循环图是处理任务系统的[调度或处理作业的应用程序的主干——尤其是那些需要以特定顺序处理的应用程序。在更高的层次上，dag 被用来表示](https://en.wikipedia.org/wiki/Directed_acyclic_graph#Scheduling)[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)，它通常被用来跟踪在 web 和移动应用程序中具有可变状态的对象。

然而，我个人最喜欢的例子是使用 Dag 来表示[依赖图](https://en.wikipedia.org/wiki/Dependency_graph)。如果你曾经不得不解决 [Gemfile](http://bundler.io/gemfile.html) 中的依赖关系，或者使用 [npm](https://www.npmjs.com) 或 [yarn](https://yarnpkg.com/en/) 之类的包管理器来处理更新包或导航依赖关系问题，那么你就是在非常抽象的层面上与 DAG 进行交互！依赖图也是一个很好的例子，说明了 Dag 是如何复杂、庞大和庞大的，以及为什么在这样的图中对节点进行排序是有用的。

所以，让我们来学习如何做吧！

### 拓扑排序

既然我们现在知道了一个有向无环图实际上可以有多庞大和复杂，那么能够对顶点进行排序并理解 DAG 中的数据是非常有用的。谢天谢地，有一种算法可以做到这一点！

<figure>[![](img/c974b7b84ee947e210a7792720ce343a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---22ORZEZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADpkPJ0uEDaXtf2t-TLHZYA.jpeg) 

<figcaption>拓扑排序:一个定义</figcaption>

</figure>

***拓扑排序算法*** 允许我们根据连接顶点的边的互连性，以特定的顺序对图形的顶点进行排序。如果两个顶点 x 和 y 存在于一个图中，并且它们之间存在有向边(x，y ),那么拓扑排序将按照我们在路径中遇到它们的方式对顶点排序:x，接着是 y。

如果我们考虑拓扑排序的排序，我们会注意到排序本身是 ***非数值*** 。换句话说，顶点的顺序是基于连接它们的*边*，而不是任何数值。

<figure>[![](img/712e9b2e2aacce28d79d9247c6132026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q9jo44u1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUOM0uS-9-LRVsoyZ8mFDIA.jpeg) 

<figcaption>拓扑排序及其产生的拓扑顺序</figcaption>

</figure>

当涉及到拓扑排序时，要记住的最重要的事情是这样一个事实，即我们只能在到达一个顶点的所有顶点都已经被访问过之后才能访问它。

例如，在这里显示的图中，我们有五个节点:v、w、x、y 和 z。为了在这个有向图上运行拓扑排序，我们需要确保，当我们排序和访问我们的顶点时，在 x 和 w 都被访问之前，不能访问节点 y。

通过查看该图，我们可以看到有两条可能的路径:v-w-x-y-z 或 v-x-w-y-z。在这两条路径中，我们仍然会在 x 和 w 之后访问节点 y。因此，这两条路径都是有效的，并且每条路径都返回有效的 ***拓扑顺序*** ，这是运行拓扑排序返回的有序顶点集。

在这个图中，我们没有一个周期。但是如果我们做了呢？这种图最简单的形式是有两个节点，每个节点相互引用，形成一个循环。下面是该图表的一个示例:

<figure>[![](img/16b540d5a4143f47c3c9524729ffac43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mXEIBSH1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYLZkuCdhFxZ6a0VhTz13RQ.jpeg) 

<figcaption>拓扑排序只能应用于 Dag 或无环图。</figcaption>

</figure>

在这个例子中，顶点 a 依赖于 b，而 b 又依赖于 a。这里的问题是，我们不能将访问“通向”我们开始的那个顶点的规则应用于我们开始的那个顶点，因为它们都通向另一个顶点。

> 拓扑排序只能应用于有向无环图或 Dag。在有圈的有向图上进行拓扑排序是不可能的，因为不清楚排序本身应该从哪里开始。

实际实现拓扑排序有几种不同的方法。然而，今天，我们甚至不需要学习任何新的，因为我们已经*直观地知道*一个！当然，我说的是*深度优先搜索*。

因此，让我们看看拓扑排序的实际应用——使用 DFS 来帮助我们！

<figure>[![](img/6d5e5cd39e3cfc132814a1bf2685ee35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ylU_HXEI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjXJpM4d0a3Ma5CB36tRKwQ.jpeg) 

<figcaption>拓扑排序 in-action！</figcaption>

</figure>

我们将使用与之前相同的图表；关于运行 DFS 来寻找拓扑顺序的重要事情是使用 DFS 完成顺序的*反向* *来组装我们的顶点。*

我们将从在起始“父”节点上运行 DFS 开始，该节点必须是 v，因为它是唯一没有通向它的边的节点。我们可以选择 w 或 x 向下遍历——哪个都没关系。在这个例子中，我们将选择 w，继续向下到 y，然后是 z。

当我们到达 z 点时，我们就进入了死胡同！因此，我们需要回溯到 y。但是，在我们回溯之前，我们将为节点 z 分配一个数字(稍后我们将使用该数字对顶点进行排序)。在这种情况下，我们将为其分配值 4，因为它将最后排序——记住，我们希望*反转 DFS 的完成顺序*。

接下来，我们将回溯到 y；由于没有地方可去，我们将重复分配数字 3 的相同步骤，然后再次回溯到 w。我们将继续这样做，直到我们到达具有*未访问*边的节点。每次我们这样做的时候，我们会根据需要对顶点进行排序。

最后，我们回到起始节点 v。当我们回溯到“父”节点时，我们会看到还有一条边没有访问:剩下的唯一一条边是 x。因此，我们将访问它，给它分配一个数字(在本例中为 1)，并通过节点 v 结束我们的搜索。最后，如果我们遵循数字 0-1-2-3-4，我们将以 v-x-w-y-z 的顺序结束。

我们做到了！我们的顶点都进行了排序，因此任何依赖于*在*之前的节点的节点，只有当它是路径中的父节点时才会被访问——它的依赖项——才会被首先访问。现在，我们的示例图非常小，但我们可以想象 npm 或 yarn 如何对更大的依赖关系图做到这一点。

所以，下次你对更新依赖关系感到害怕时，只要记住:这只是一个有向无环图！你能行的。

### 资源

拓扑排序有几种不同的方法，但我发现从深度优先搜索和图中的循环的上下文中理解它是最容易的。令人欣慰的是，有一些很好的资源可以分解拓扑排序在其他环境中的工作方式，更不用说所有关于有向无环图的大量资源了！如果你想继续学习，这里有一些我最喜欢的。

1.  [使用深度优先搜索的图拓扑排序](https://www.youtube.com/watch?v=n_yl2a6n7nM&t)，Sesh Venugopal
2.  [深度优先搜索(DFS)，拓扑排序](https://www.youtube.com/watch?v=AfSk24UTFS8&t=201s)，麻省理工学院开放式课程
3.  [拓扑排序——图论](https://www.youtube.com/watch?v=jonc-AGbPQo&t)，NerdFirstTV
4.  [图遍历](http://www.cs.cornell.edu/courses/cs2112/2012sp/lectures/lec24/lec24-12sp.html)，安德鲁·迈尔斯，康奈尔大学计算机科学系
5.  深度优先搜索，史蒂文·斯基纳教授
6.  [拓扑排序](http://www.cs.utoronto.ca/~tabrown/csc263/2014W/week9.pdf)，特雷弗·布朗教授

* * **