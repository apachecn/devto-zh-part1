# 透视理解快速排序[第二部分]

> 原文：<https://dev.to/vaidehijoshi/pivoting-to-understand-quicksort-part-2>

这是快速排序两部分系列的第二部分。如果你还没看过这个系列的 [*第一部*](https://dev.to/vaidehijoshi/pivoting-to-understand-quicksort-part-1) *，我推荐你先看看！*

在本系列的第 1 部分中，我们简要介绍了快速排序算法的工作原理。如果您需要快速复习一下，这个算法有两个重要方面:一个 pivot 元素和围绕 pivot 的两个分区。

我们将记住快速排序是通过选择一个支点来起作用的(记住，这只是*有点*随机！)，并对元素中的剩余项进行排序，使小于枢轴的项位于枢轴的左侧或前面，而大于枢轴的项位于枢轴的右侧或后面。这两半成为分区，算法递归地调用这两个分区，直到整个列表被分成单个条目的列表。然后，它将它们重新组合在一起。

快速排序得到了广泛的应用和研究，它通常是“最有效的算法”的同义词。然而，因为有太多关于这个算法的知识需要了解，尽管我们上周已经讲了很多，我们仍然有太多的问题没有回答！

今天，我们将深入了解这些秘密，并试图理解是什么让快速排序如此快如闪电…以及为什么，有时不是这样！

### 好算法变坏了

我们一次又一次听到的是，在未排序的列表中，pivot 是一个*有点*任意的元素。我们知道，我们选择的*和*作为支点可能略有随意，但我们应该真正瞄准“最中间”的元素，或者接近整个系列的 ***中间值*** 。

这很重要的一个原因是，我们希望我们的两个分区——我们将递归地调用 quicksort 大小相等。上周，我们了解到两个大小不相等的分区可能会产生很大的问题。但是…我们还是不知道为什么！

是时候找出答案了。当然，说明为什么这是一个问题的最好方法是使用一个例子！在下图中，我们有一个实际上已经*接近*排序的列表。假设我们选择最后一个元素 16 作为我们的枢纽元素。

<figure>[![](img/5c55c93a9e889d3a42b6580ba12fb08b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1pC1g8mq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAuOVk7USUwVhrbmejkg5sQ.jpeg) 

<figcaption>如果我们不明智地选择我们的支点，我们就必须将它与所有元素进行比较！</figcaption>

</figure>

为了进行交换，我们将创建两个指针引用，并将所有元素与轴心进行比较。

但是你猜怎么着？所有的元素都比我们的枢轴小！所以，我们不会真的交换，对吧？

好的，这仍然很好，我们在这里做( *n* -1)比较，其中 *n* 是要排序的元素总数。

但是第二次会发生什么呢？我们的支点是 15 度。同样，当我们开始比较所有的元素时，几乎所有的元素都比 pivot 元素小。

不仅我们的分区不平衡，而且还有其他不好的事情发生！每次通过数组时，我们只对*进行少一次的*比较。这不是很好，是吗？其实很可怕！如果你对此感到畏缩，并且觉得你以前见过这种情况———那么，你是对的！我们在这里进行的比较的数量实际上相当类似于[的冒泡排序算法](https://dev.to/vaidehijoshi/bubbling-up-with-bubble-sorts)，它有一个二次运行时间，或*O(n)*。

如果我们继续在这里对列表进行排序，我们最终会执行*n*比较操作。这一点都不理想——这就是为什么它是一个很好的例子，说明了如果我们没有正确选择枢轴会发生什么！如果我们选择一个不接近我们要排序的集合的中间值的枢轴，我们将不得不将其与几乎所有的元素进行比较，因为它们要么都比枢轴大，要么都比枢轴小。

<figure>[![](img/75c0bfe508e632d9a26a6302ea913eee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G0JcyDCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsYvLxsc-TZTnbA9oPoPSyw.jpeg) 

<figcaption>快速排序的时间复杂度取决于分区和列表的排序方式。</figcaption>

</figure>

在某些情况下，我们很可能会陷入选择不在数据集中间值附近的枢轴的陷阱。事实上，我们刚刚就掉进了这个陷阱！

> 对于几乎或完全排序的数组，快速排序的效果最差。换句话说，已排序或接近排序的列表的平均运行时间是 quicksort 的最坏情况运行时间:O(n)。

如果我们知道我们将要处理的数据大部分是经过排序的，那么记住这一点尤其重要。在这种情况下，我们希望远离快速排序，因为它将需要很长时间来运行。

### 务实排序

好了，现在我们已经讨论了交换两个分区中的元素和选择一个枢纽元素的细节，让我们来看一个快速排序算法！

虽然我通常在我的帖子中使用 Rosetta Code 的[算法](http://rosettacode.org/wiki/Sorting_algorithms/Quicksort)作为例子，但对于这个特定的算法，我发现了另一个对我来说更有意义的实现。下面的算法是由 Nicholas Zakas 编写的[，他有一些非常棒的](https://www.nczonline.net/blog/2012/11/27/computer-science-in-javascript-quicksort/)[资源和关于用 JavaScript 实现计算机科学概念的博客帖子](https://www.nczonline.net/blog/tag/computer-science/)！我真的很喜欢他的实现(谢谢你，尼克！)，下面我用的是他的代码。我对他的功能做了一点调整，重新命名了一些东西，添加了我自己的注释，以及一些 console.log，这些在我们运行代码时会派上用场。

所以，言归正传。我们将首先从最外部的函数开始。这是实际的 quickSort 函数本身，它接受三个参数:一个要排序的条目数组，一个 leftIndex 和 rightIndex，它们是用来跟踪哪些元素将(可能)被交换的左右引用。

```
function quickSort(items, leftIndex, rightIndex) {
  // Declare an index that will be our pivot reference.
  var pivotIndex;

  // If the array has only one item, it's already sorted!
  // If it has no items, we don't want to try to sort it!
  if (items.length > 1) {
    // As long as the array has two items, we can parition it.
    pivotIndex = partition(items, leftIndex, rightIndex);

    console.log('** pivot is: ', items[pivotIndex]);

    // If the left reference hasn't been incremented to
    // reach the pivot yet, we want to keep comparing it.
    if (leftIndex < pivotIndex - 1) {
      quickSort(items, leftIndex, pivotIndex - 1);
    }

    // If the right reference hasn't reached the 
    // pivotIndex yet, we need to keep comparing it.
    if (pivotIndex < rightIndex) {
      quickSort(items, pivotIndex, rightIndex);
    }

  }

  return items;
} 
```

这个 quickSort 函数发生了相当多的事情，我们来分解一下。首先，我们声明一个索引变量，它将成为我们的 pivot 引用。然后，我们实际上只运行这个算法，如果被传入的数组中有不止一个元素，这是有意义的，因为如果它是空的或者只有一个元素，我们甚至不想麻烦排序它！

我们会注意到，在外部 if 中有两个 if 条件语句:这两个语句中的逻辑负责确定 rightIndex 引用和 leftIndex 引用是否仍然需要比较，或者它们是否可以递归地快速排序成更小的部分。

但是分区的工作呢？嗯，有一个功能！我们会注意到我们在第 9 行调用了 partition。这个函数可能是这样的:

```
// The partition() method takes a list of items, and a left
// reference, as well as a right reference. Both left and right
// are indexes to indicate where the pointers should start.
function partition(items, left, right) {
  // Find the pivot by adding the two indexes together
  // and dividing by two (the middle element, effectively).
  var pivot = items[Math.floor((right + left) / 2)];
  var l = left;
  var r = right;

  console.log('** pivot is: ', pivot);
  console.log('** left is: ', items[left]);
  console.log('** right is: ', items[right]);

  // Once the left reference is greater than the right reference,
  // we have finished sorting this set of items, and we can return.
  while (l <= r) {
    // If the left pointer is less than the pivot, increment it.
    // In other words, move the pointer to the right.
    while (items[l] < pivot) {
      l++;
      console.log('l is now pointing to: ', items[l]);
    }

    // If the right pointer is greater than the pivot, decrement it.
    // In other words, move the pointer to the left.
    while (items[r] > pivot) {
      r--;
      console.log('r is now pointing to: ', items[r]);
    }

    // If the left pointer is larger than the pivot, and the right
    // pointer is not bigger than the pivot, swap the two elements.
    if (l <= r) {
      console.log('** now swapping l and r pointers: ', items[l], items[r]);

      swap(items, l, r);

      // After swapping, increment/decrement the pointers respectively.
      l++;
      r--;

      // console.log('l is now pointing to: ', items[l]);
      // console.log('r is now pointing to: ', items[r]);
    }
  }

  // The left item becomes the new pivot element.
  return l;
} 
```

正如注释所解释的，分区函数接收我们试图分区的项目，以及表示项目列表的开始和结束的左和右索引。

我们可以看到两个 while 循环嵌套在一个更大的 while 循环中。这就是我们比较枢轴的左右引用的工作，这是最终决定两个项目是否需要交换的原因。

我们还可以看到，分区函数正在调用交换函数！上周我们简要地看了一下这个。这就是交换函数的作用:

```
function swap(items, leftPointerIndex, rightPointerIndex){
  // Create a temporary reference for the left item.
  var tempReference = items[leftPointerIndex];

  // Move left item to the index that contains right item.
  // Move right item to the temporary reference.
  items[leftPointerIndex] = items[rightPointerIndex];
  items[rightPointerIndex] = tempReference;
} 
```

与我们刚刚看到的两个更大的函数相比，swap 函数几乎没有什么责任:它创建一个 tempReference，使用它来交换两个 index 处的两个元素，我们将这两个元素作为参数传递给它。这是一个非常好的逻辑封装，因为它根本没有任何快速排序或分区函数的概念！

好，让我们试着运行这段代码。我们将尝试对一组条目进行排序，如下所示:[19，22，63，105，2，46]。

```
var items = [19, 22, 63, 105, 2, 46];
quickSort(items, 0, items.length - 1);

> ** pivot is:  63
> ** left is:  19
> ** right is:  46
> l is now pointing to:  22
> l is now pointing to:  63
> ** now swapping l and r pointers:  63 46
> ** now swapping l and r pointers:  105 2
> ** pivot is:  105
> ** pivot is:  22
> ** left is:  19
> ** right is:  2
> l is now pointing to:  22
> ** now swapping l and r pointers:  22 2
> r is now pointing to:  2
> ** pivot is:  46
> ** pivot is:  19
> ** left is:  19
> ** right is:  2
> ** now swapping l and r pointers:  19 2
> ** pivot is:  19
> ** pivot is:  46
> ** left is:  46
> ** right is:  22
> ** now swapping l and r pointers:  46 22
> ** pivot is:  46
> ** pivot is:  105
> ** left is:  105
> ** right is:  63
> ** now swapping l and r pointers:  105 63
> ** pivot is:  105
>> (6) [2, 19, 22, 46, 63, 105] 
```

厉害！这是怎么回事？首先，我们将数组以及它的开始和结束索引传递给外部快速排序算法。我们可以看到，quicksort 的这个特定实现选择了这个列表中间的元素作为它的枢纽；在我们的例子中，元素是 63。回想一下，数据透视表是第一个、最后一个还是中间元素并不重要，只要我们不处理快速排序的最坏情况。

请注意，我们是如何不断增加左指针的值，直到遇到位于左引用的元素大于位于右引用的元素的情况。如果我们观察左的值是如何变化的，我们可以看到这种情况的发生。第 5 行，左指 19；在第 7 行，我们看到这个引用增加到指向 22。只有当左指向 63，右指向 46 时，算法才真正能够交换——这正是我们在第 9 行所做的。一旦我们理解了第一次通过阵列是如何工作的，就有希望更容易辨别在后续的通过中发生了什么。

当然，这只是实现 quicksort 的一种方式，我倾向于喜欢它，因为它很容易跟踪正在发生的事情。我们也可以用函数和命令的方式写同样的算法。我们甚至可以尝试迭代地实现它，而不直接使用递归。但这可能是另一天的挑战！

### 权衡排序的成本收益

现在，我们对 quicksort 的实现有了更多的了解，还有最后一点需要讨论:quick sort 到底有多好，我们什么时候想使用它？

让我们开始看看 quicksort 算法与其同类算法相比如何。我们已经熟悉了快速排序的时间复杂性。它在运行时的平均性能和最佳性能与合并排序相同:它在 **linearithmic** 时间内运行，或 *O(n logn)* 。那么这个算法还有什么其他的定义特征呢？

<figure>[![](img/d71f14645f391e48bf4b24b8c62b5ea4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Z0zNtTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEiF3qR4X97afEFrKw1EWYQ.jpeg) 

<figcaption>快速排序与其他排序算法相比如何？</figcaption>

</figure>

根据我的研究，quicksort 的空间复杂度是一个值得讨论的话题。如果我们需要刷新，空间复杂度是由一个算法运行需要多少额外的内存或空间决定的。quicksort 的两难之处在于，它*确实*需要一定量的额外空间——每个递归算法在继续将数组划分和排序为单元素列表时分配左右指针引用的空间。

然而，问题就在这里:尽管 quicksort 需要的空间比传统的常量或 *O(1)* 多，但它并不需要比这多得多的空间。它需要额外的*O(Logan)*空间量，这仍然很小，但从技术上讲，这比 *O(1)* 常量空间要多。有些人似乎认为这使它成为一个不合适的算法，但是我读过的大多数文本和课程材料都暗示需要最小空间的算法，包括*O(Logan)*，仍然被认为是合适的。所以我们会说 quicksort 是一种 ***原地*** 排序算法。

Quicksort 与 merge sort 有一个主要区别，这最终成为这些超级相似算法之间的一个定义点。事实上，快速排序是一种 ***不稳定的*** 算法，这意味着它不能保证在重新排序时保持元素的顺序；在未排序的数组中完全相同的两个元素可能在排序后的数组中以相反的顺序出现！稳定性最终导致人们选择合并排序而不是快速排序，这也是合并排序明显胜出的一个方面。

类似地，因为 quicksort 可以在不创建新的或重复的列表的情况下对项目进行排序，所以它不需要外部存储器；相反，它可以在不使用外部存储器的情况下存储其整个数据集，使其成为一种 ***内部*** 排序算法。我们已经知道，quicksort 倾向于从自身内部对所有自身使用递归，这意味着我们也可以将其归类为一种 ***递归*** 算法。并且，我们还知道它使用了类似<和>的比较运算符，所以我们也可以确定它是一个 ***比较*** 算法。

这些特点是理解为什么那么多人很久以前就信了 quicksort 的关键，从此一去不返。基于这个两部分的系列，我们已经有了确定 quicksort 什么时候是合适的工具的知识！

<figure>[![](img/f5e42442eecf8c5ce5db3489795623af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7C7ZqBMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABjEafvcnl_p3mi1sOBcsgw.jpeg) 

<figcaption>我们如何进一步优化快速排序？</figcaption>

</figure>

如果我们发现自己符合以下条件，快速排序通常是一个很好的选择:

1.  我们不在乎保持项目的顺序(稳定性对我们来说并不重要)。
2.  我们需要使用一种算法，可以将我们所有的数据分类到内存中，而不使用任何外部存储器。
3.  我们确信，我们永远也不会处理已经排序的数据，甚至是大部分已经排序的数据。

事实证明，程序员非常喜欢快速排序，他们甚至想出了如何进一步优化这个算法的方法！

我们可以通过并行运行递归调用来优化快速排序。

无论如何，我们都必须在数据集的左分区和右分区上调用 quicksort。酷的是，因为在元素的初始划分之后，这两个分区都不需要相互比较，所以我们可以在两个分区上并行运行这两个递归调用。

当我们并行运行两个 quicksort 调用时，我们实际上是在生成两个不同的任务，以便同时运行它们。我们独立地对阵列的每个分区进行排序，使用相同的空间量，但时间只有以前的一半。这就是通常所说的 ***并行快速排序*** 。

另一个很好的优化是更聪明地选择枢轴。我们不只是选择第一个或最后一个元素——这是我们一直在做的——我们可以用我们的算法变得更聪明一点。

> 我们可以查看列表中的最后几个元素，并从中选择中间的元素。这让我们对列表中的项目类型有了更好的了解，然后我们可以做一点数学计算来选择最佳的项目作为枢纽。

很多时候，即使一个*优化的*快速排序也不能满足我们所有的条件。那么程序员如何处理这种情况呢？为什么，他们当然有条件地使用快速排序！

例如，Node 在其 Array.sort 函数下使用 quicksort 但是，对于较短的数组(在 node 的例子中，长度小于或等于 10)，它使用插入排序！你可以看到他们在 [v8 的源代码](http://://github.com/v8/v8/blob/master/src/js/array.js#L709)中使用的逻辑。

快速排序如此受欢迎，以至于如果我们开始留意它，我们会发现它就在我们周围。比如 Ruby 的解释器在幕后使用了 quicksort 如果我们深入挖掘，我们会发现[一个叫做 ruby_qsort 的方法](https://github.com/ruby/ruby/blob/f5052d45be1b564a683c347dd72fd2f7b8638fd8/util.c#L344-L492)，它拉开了这个抽象背后的帷幕。许多现代浏览器在它们不同版本的排序方法中使用了 quicksort 的实现！事实上，你现在使用的浏览器很可能在它的排序代码中隐藏了快速排序的内部定义。当然只是看你自己去找。

所以，往前走——你现在完全知道要找什么了！

### 资源

Quicksort 可以用许多不同的方式编写——命令式的、函数式的，甚至是迭代式的(不是递归式的！).每种实现都有其优点和缺点，在编写快速排序算法的过程中，理解不同的设计决策非常重要。如果您想尝试编写自己的代码，或者想了解不同的实现以及是什么使它们高效，请查看下面的参考资料！

1.  [快速排序的效率](https://www.youtube.com/watch?v=aMb5GHPGQ1U&t=112s)，Udacity
2.  [排序算法/快速排序](http://rosettacode.org/wiki/Sorting_algorithms/Quicksort)，罗塞塔代码
3.  JavaScript 中的计算机科学:流沙
4.  [并行快速排序算法](https://www.uio.no/studier/emner/matnat/ifi/INF3380/v10/undervisningsmateriale/inf3380-week12.pdf):奥斯陆大学
5.  [快速排序是否到位](https://stackoverflow.com/questions/22028117/is-quicksort-in-place-or-not)，堆栈溢出

* * *

*本帖最初发表于[medium.com](https://medium.com/basecs/pivoting-to-understand-quicksort-part-2-30161aefe1d3)T3】*