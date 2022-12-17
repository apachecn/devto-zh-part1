# 在 Javascript 中合并数组

> 原文：<https://dev.to/bengreenberg/merging-arrays-in-javascript-8gn>

在我的下一期[文科程序员的编码概念](https://dev.to/benhayehudi/whats-the-deal-with-binary-search) ( *即不是来自数学或科学背景的程序员*)中，我们将看看在 Javascript 中合并排序数组。您将遇到的一个常见挑战是表示两个有序整数数组，并且需要将它们合并成一个大型有序数组。你会怎么做？我们来看一个方法。

在我们的场景中，我们有两个有序数字列表:

```
let firstList = [4, 6, 8, 9]
let secondList = [2, 3, 5, 7] 
```

Enter fullscreen mode Exit fullscreen mode

在开始编写我们的解决方案之前，如果您在任何其他情况下面临这个问题，您会首先做什么？你可能会创建一个新的列表，从每个列表中取最小的数，并把它添加到你的新列表中。我们该如何编码？在开始的时候，我们只想从每个列表中找到最小的数字，然后删除它，这样我们的原始列表就变得越来越小，越来越简单:

```
function getSmallestThenRemove(firstList, secondList) {
    let smallestFirstList = firstList[0];
    let smallestSecondList = secondList[0];

    if (smallestFirstList < smallestSecondList) {
        return firstList.shift()
    } else {
        return secondList.shift()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中，我们通过引用每个现有数组中的第一个索引位置来定位最小的数字，并创建两个新变量来保存该数据。在另一篇文章中，我们将讨论如何用两个未排序的数组实现一个相似的函数。)然后我们检查哪个最小的数更小。如果第一个列表的最小数字小于第二个列表的最小数字，我们返回没有那个数字的第一个列表，如果相反，我们返回没有最小数字的第二个列表。

现在我们有了一个函数，它为我们找到最小的数字，从列表中删除它，并返回没有最小数字的原始列表，接下来我们需要做什么？

下一步是创建另一个函数，当它遍历两个独立的数组时，从自身内部调用`getSmallestThenRemove()`，当它们从原始数组中移除时，将每个最小的数添加到一个新的合并数组:

```
function mergeLists(firstList, secondList) {
    let newList = [];
    let iteratedNum;

    while (firstList.length != 0 && secondList.length != 0) {
        let iteratedNum = getSmallestThenRemove(firstList, secondList)
        newList.push(iteratedNum)
    }
    return newList.concat(firstList).concat(secondList)
} 
```

Enter fullscreen mode Exit fullscreen mode

在`mergeLists()`函数中，我们正在做几件事:

1.  我们创建一个空数组，这将是我们的新排序数组将居住的地方。
2.  我们创建一个变量`iteratedNum`，它将保存我们正在处理的当前数字。
3.  我们遍历原始列表，直到它们变空。每次，我们将`iteratedNum`的值定义为`getSmallestThenRemove()`的返回值，并将该值推入我们的`newList`。
4.  最后，我们通过连接`firstList`或`secondList`的剩余部分来返回`newList`，因为一旦我们完成了函数，我们将剩下一个空的原始列表，另一个包含新排序数组的剩余部分。

因此，回到我们最初的两个列表，一旦我们运行我们的新函数，我们将返回如下:

```
let firstList = [4, 6, 8, 9]
let secondList = [2, 3, 5, 7]

mergeLists(firstList, secondList)

// [2, 3, 4, 5, 6, 7, 8, 9] 
```

Enter fullscreen mode Exit fullscreen mode

下周回来查看另一期文科程序员的编码概念！