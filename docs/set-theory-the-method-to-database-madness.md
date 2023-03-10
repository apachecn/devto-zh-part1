# 集合论:解决数据库疯狂的方法

> 原文：<https://dev.to/vaidehijoshi/set-theory-the-method-to-database-madness>

现在我们终于完成了这个系列的三分之一，事情终于开始变得明朗起来。当然，我们知道很多不同的数据结构，它们是如何工作的，哪些是快速的，以及为什么某些数据结构比其他数据结构更有助于解决特定的问题。

但是如果我们不知道它们在现实生活中是如何被使用的，那么知道这些几乎是没有意义的。这就像学习几何:这可能看起来毫无意义，直到有一天，你醒来，意识到你实际上必须计算一个房间的平方英尺，因为你想重新铺地毯！(好吧，我实际上从来没有这样做过，但我可以想象，如果我不得不这样做的话，几何作为一个概念通常会很有帮助。)

今天将会有很多东西聚集在一起，因为我们将要学习一种在理论上几乎是教条式的数据结构，但在实践中却无处不在。事实上，你可能已经以某种形式使用过这种结构，你可能在中学数学课上接触过它。

那么，我说的是什么数据结构呢？为什么，我指的当然是全能**集**啦！

### 无恐集论

在我们进入集合的实际实现之前，我们首先需要了解它们究竟来自哪里。这意味着是我们深入理论——集合论的时候了！但是，不用担心:你很有可能在某种程度上使用过集合论。事实上，你可能知道集合论的另一个名字:**维恩图**。维恩图实际上在 20 世纪 60 年代才被纳入“集合论课程”,因为它是说明集合之间简单关系的有效方法。

好了，现在我们确定集合论不会那么可怕了，我们最好弄清楚它到底是什么，确切地说！一个*集合*实际上是一个数学概念，我们将集合相互关联的方式被称为**集合论**。

> 一个**集合**只不过是一个完全没有重复的无序元素集合。

这个定义有三个重要部分:****元素*** 和 ***无重复*** 。实际上，这三个词几乎包含了集合的全部定义；如果我们能记住这一点，我们就基本上了解了集合如何工作的一切。*

 *但是我们一会儿会回到为什么这很重要。首先，让我们来看一些场景。我们知道维恩图很好地表示了集合，所以对于我们的例子，我们将查看两个集合:一个是我读过的一些书的集合，另一个是我拥有的一些书的集合。

<figure>[![](img/3337e4fed1287eb8d2533b4b4589278a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C6B_17tM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AV0Zl-nCUi32G8bozk9WFgw.jpeg) 

<figcaption>我读过的书与我自己的书</figcaption>

</figure>

因为我们熟悉[维恩图](https://en.wikipedia.org/wiki/Venn_diagram)的概念，我们知道绿色的中间部分(两组相交的地方)代表我读过的书*和我拥有的书*。我们也知道上面画的两套存在于世界上所有书籍的更大的组中！

维恩图是集合论的一个很好的介绍，因为它使下一部分更容易解释。想象一下，我们想用某种结构来表示这两组数据。嗯，我们已经知道我们需要把它们分成两组:*我读过的书*，和*我拥有的书*。为了简单一点，我们将我读过的*书*称为**集 X** ，我拥有的*书*称为**集 Y** 。如果重新配置成数据结构，维恩图中的这两个集合也可以重写为如下形式:

<figure>[![](img/41849fd68c2eb74b39a8e43a040a34a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---xwm3Vun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9XNbdyPRdSpwbRABlpGeJw.jpeg) 

<figcaption>集合是不同的、唯一的对象的集合，从不包含重复的值。</figcaption>

</figure>

我们会注意到，集合 X 和集合 Y 看起来都有点像对象或散列:元素没有索引或任何排序。它们也没有任何重复的值，这是它们成为一个集合的部分原因。请记住，集合是唯一的、无序的对象的集合，这意味着我们永远不会在集合中找到重复的值。

### 无痛(设定)操作

那么，既然我们已经用数据结构格式编写了这些集合，我们还能做什么呢？嗯，我们可以对它们进行一些操作！对集合执行的两个最重要的操作是**交集**和**并集**。

<figure>[![](img/da6818dea9feb6096f280162102c750c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WNBcK_BB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1hRlR0-wWkomygt1_6TVSA.jpeg) 

<figcaption>基本操作</figcaption>

</figure>

两个集合的交集通常用这样的简写来表示:***X \u Y***。交叉点代表两个集合——你猜对了——*的交叉点*！换句话说，它产生存在于两个集合中的所有元素。在我们的例子中，集合 X 和集合 Y 的交集是两者中存在的所有元素。记住交集如何工作的一个好方法关键字是单词**和**:同时存在于 X 和 y 中的元素。在这种情况下，这意味着“代码完成”和“牛奶&蜂蜜”。即使它们存在于两个集合中，因为集合只能包含唯一的*值，所以我们不重复它们；这些书名中的每一个在该集中只出现一次。*

两个集合的并集通常用这样的简写表示:***X \u Y***。这种结合代表了两个集合的整体，或者说当两个集合被*结合在一起的时候。换句话说，它产生存在于两个集合的*或*中的所有元素。记住交集如何工作的一个好方法关键字是单词**或**:同时存在于 X 或 y 中的元素。在这种情况下，这将意味着所有八本书的标题！需要记住的重要一点是，尽管“Code Complete”和“Milk & Honey”存在于两个集合中，但它们在集合 X 和集合 Y 的并集中只能出现一次，因为集合只能有不同的值，不能包含重复的值。*

如果我们将交集和并集应用到之前的维恩图中，我们的图看起来会像这样:

<figure>[![](img/f1c031c4629c194a44404551facd7071.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ElDvRutF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzQlN1OgLzQtEc5irFDGCUw.jpeg) 

<figcaption>交集 vs 并集</figcaption>

</figure>

好了，是时候让事情变得复杂一点了！交集和并集是伟大的，但它们只是触及了集合论的表面。出于我们的目的，我们还需要熟悉一些其他操作。事实证明，有两种运算在计算机科学中出现得相当多:**设置差异**和**相对补码**。我们将在下一节了解它们是如何发挥作用的，但首先，让我们弄清楚它们是如何工作的！

<figure>[![](img/27c622bafc69abe7b864158b9bceb943.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AgiuX4oW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8FySrL8_5SD6OvZmZVvU9Q.jpeg) 

<figcaption>设置差异和相对补语</figcaption>

</figure>

集合差异就是我们如何计算两个集合之间的差异。换句话说，我们可以确定没有包含在 *other* 集合中的任何元素的集合是什么样子。另一种写法是**X-Y**。在此处所示的示例中，集合 X 和集合 Y 之间的差异导致集合 X 中存在但集合 Y 中不存在的所有元素，或者字母 *C、Z、*和 *W* 。

相对补语基本上是集合差的反义词。例如，Y 相对于 X 的相对补码将返回集合 Y 中没有出现在集合 X 中的所有元素。我们可以使用简写**Yˇ–X**来表示相对补码，这实际上会导致返回与**Y–X**完全相同的集合。在我们的例子中，集合 Y 小于集合 X，在我们的例子中，唯一存在于 Y 中而不存在于 X 中的是数字 _ 2_。

> 实际上，我们只是简单地从集合 Y 中减去集合 X，然后回答这个问题:*什么东西在 Y 中存在而在 X 中不存在？*

你可能已经注意到，在一些例子中，我们处理的是字符串，而在其他例子中，元素是字母，有时甚至是数字。这引出了一个重要的问题:集合可以包含任何类型的元素或对象！你可以把它们想象成散列:它们可以保存任何项目，只要它在集合中只出现一次。

好了，让我们看看最后一个操作——所有操作中最复杂的一个。但是我们能处理好！

有时，当我们有两个集合时，我们可能希望找到两个集合的交集的反方。换句话说，我可能想要找到我拥有的所有书籍，以及我读过的所有书籍，但不要找到这两者之间的交集。我们称这个子集为什么？我们怎么找到它？

嗯，我们在这种情况下寻找的合适术语是我们两个集合的对称差，有时也被称为 T2 析取并。对称差产生存在于两个集合中的所有元素，但是*不存在于它们的交集(X-Y)处。*

让我们看一个例子，它应该有助于阐明我的意思:

<figure>[![](img/32924672cd4faecc3242a82f94fb17b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zU0BlBgl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN3Z94nCNu8IHsFenIAELJw.jpeg) 

<figcaption>对称差集</figcaption>

</figure>

在上面的例子中，对称差基本上与寻找集合 X 和集合 Y 的相对补相同。如果我们对它进行超级数学化，寻找对称差与寻找集合 X 和集合 Y 的相对补的并集是相同的。我们可以将它写成:**X–Y =(X \u\u- Y \u\u( Y \u- X)**。

但是不要让那迷惑你！

> 为了找到两个集合的对称差，我们真正需要做的就是问自己:*有哪些元素存在于集合 X 中而不存在于集合 Y 中，有哪些元素存在于 Y 中而不存在于 X 中？*换句话说:哪些元素对于每个集合是唯一的，并且不会同时出现在两个集合中？

在上面的例子中，数字 *1、2、*和 *3* 出现在两个集合中。但是，字母 *A、B、C* 和 *X、Y、Z* 只出现在集合 X 或集合 Y 内，因此是两个集合的对称差。

好吧，这是一大堆理论上的东西。让我们看看这个理论在实践中如何发挥作用，好吗？

### 集我们周围的一切

希望到现在为止，你还在想学习集合的意义是什么。我不怪你:这是个好问题！也终于到了回答的时候了。

你猜怎么着？片场到处都是*。在 [Java](https://docs.oracle.com/javase/7/docs/api/java/util/Set.html) 、 [Python](https://docs.python.org/2/library/sets.html) 、 [Ruby](http://ruby-doc.org/stdlib-2.4.1/libdoc/set/rdoc/Set.html) 、甚至 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 中，它们是我们可以随时使用的实际数据结构！你甚至可以猜出每种语言允许我们在集合上执行的一些方法。*

 *让我们快速看一下，以 JavaScript 为例:

```
var s = new Set();

s.add(2); 
// Set { 2}

s.add(45); 
// Set { 2, 45}

s.add(45); 
// Set { 2, 45}

s.add('hello world!'); 
// Set { 2, 45, 'hello world!' }

s.has(2); 
// true

s.has('cats'); 
// false

s.size; 
// 3

s.delete(45); 
// Set { 2, 'hello world!' }

s.has(45);    
// false 
```

显然，一些方法名称会随着语言的不同而变化。比如 Ruby 对 has 的[实现](http://ruby-doc.org/stdlib-2.4.1/libdoc/set/rdoc/Set.html#method-i-include-3F)叫做 include？，但是从一种语言到另一种语言，这个概念是相当相似的。Python 版本的 sets 实际上允许你[调用方法](https://docs.python.org/2/library/sets.html)，比如交集、并集和 symmetric_difference！

但是，布景有什么用呢？我的意思是，我们可以在所有这些语言中使用它们，但是它们什么时候有用呢？

<figure>[![](img/498db7c473f8a21e663efff2f3ad2198.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3s3618K7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Atv7QU64uprYlACDS_JBNDg.jpeg) 

<figcaption>集合运算</figcaption>

</figure>

嗯，首先，他们可以很省时。

还记得所有那些复杂的运算吗，比如交集、并集和差集？你猜怎么着？我们运行这些复杂操作所花费的时间完全取决于两个集合的长度。这是因为为了找到这两个集合的交集、并集或差/补集，我们必须有效地遍历被比较的两个集合的整个长度。通常，即使是巨大的集合也不会花那么多时间来遍历。

但是基本操作呢？向其中一个集合添加一个元素，删除它，或者甚至在其中查找一个元素呢？嗯，所有这些操作都需要**恒定时间**，或者 *O(1)* 。这非常强大，通常意味着集合可能是比字典或散列更好的结构！

但是，等一下:所有这些集合运算怎么可能这么快？！这是怎么回事？嗯，事实证明，很多集合实际上都是由[哈希表](https://dev.to/vaidehijoshi/hashing-out-hash-functions)在幕后实现的！(看，我向你保证这一切都会实现的！)我们已经知道了散列表，但是为什么它们是集合实现的良好框架呢？

<figure>[![](img/7b6786944565bb4ec1c903a3992cad8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hVXFj142--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AITSu0KNH0fKrF4oVHhKp7g.jpeg) 

<figcaption>哈希表经常被用来实现集合！</figcaption>

</figure>

嗯，有几个很好的理由:**首先**，根据我们对散列表的了解，它们对于每个元素总是有唯一的键。这对集合来说很好，因为集合中只能有唯一的值。**第二个**，在哈希表中，顺序并不重要，就像在集合中顺序并不重要一样。**最后**，哈希表提供了一个 *O(1)* 常量访问时间，这对于在集合上执行的基本操作来说是非常理想的。

好吧，散列表是很好的集合。而集合是大多数语言免费给我们的数据结构。但是当我开始这个帖子的时候，我告诉过你 set 是*无处不在*，对吗？我觉得我应该告诉你一个小秘密，这个秘密会(希望)让你大吃一惊:

> 关系数据库几乎完全基于集合论。

事实上，如果您曾经使用或查询过数据库，或者不得不编写 SQL，您可能对在表的交集处查找记录的想法很熟悉。

<figure>[![](img/7f3c6f3ec6d15e130e995a9bfc2ae2f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gvMWBSA7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyUZd7HpDho0S0J8AosDAfw.jpeg) 

<figcaption>关系数据库完全基于集合理论</figcaption>

</figure>

这只不过是我们开始这篇文章时所用的维恩图集的一个抽象版本！事实上，即使是最复杂的 SQL 语句也不过是对集合的操作。

<figure>[![](img/77833e90733bcd73e3cdcb794e7671d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7lkfvJPd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AYhYiJJnQLr5Z7PBKNakN2Q.png)

<figcaption>[http://stack overflow . com/questions/406294/left-join-vs-left-outer-join-SQL-server](http://stackoverflow.com/questions/406294/left-join-vs-left-outer-join-in-sql-server)</figcaption>

</figure>

一个`SQL INNER JOIN`就是两个集合的*交集*。

求两个表的`LEFT JOIN`无非是求两个表的*集合差*或者*相对补码*。

一个调用`FULL OUTER JOIN`的 SQL 查询只是返回两个集合的*并集*。

还有那个超级复杂的`FULL OUTER JOIN`两个表上一个键都是`NULL`？(说到写 SQL 语句也被称为我存在的克星？)那只是两个表的*对称差*或者*析取并*。

多神奇啊。！所有这些看似无聊的集合论，一旦付诸实践，会让数据库看起来像是有史以来最酷的东西。我的朋友，这本身就是一个真正的壮举！

### 资源

集合论在计算机科学的各个领域都相当广泛，从它在关系数据库中的使用，到存在于各种语言中的数据结构，包括 Python、Ruby、JavaScript 和 Java。有很多好的资源可以帮助你更好地理解集合论；这里有几个让你开始！

1.  [SQL Server 中的左连接与左外连接](http://stackoverflow.com/questions/406294/left-join-vs-left-outer-join-in-sql-server)，堆栈溢出
2.  [集合论](https://mariadb.com/kb/en/sql-99/set-theory/)，MariaDB
3.  [Python 操作的复杂性](https://www.ics.uci.edu/~pattis/ICS-33/lectures/complexitypython.txt)，Richard E. Pattis 教授
4.  [集合论——计算机程序的设计](https://www.youtube.com/watch?v=x7ARbhM7kuw)，Udacity
5.  [集合论|简介](http://quiz.geeksforgeeks.org/set-theory/)，GeeksforGeeks

* * *

*本帖最初发表于[medium.com](https://medium.com/basecs/set-theory-the-method-to-database-madness-5ec4b4f05d79)T3】***