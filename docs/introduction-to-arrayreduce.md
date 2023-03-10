# 阵列介绍。减少()

> 原文：<https://dev.to/misterwhat/introduction-to-arrayreduce>

# “还原”数据的概念

在这个介绍中，我将使用一个假想的音乐库(由曲目和播放列表组成)应用程序作为例子。基本概念也适用于任何其他类型的应用程序。

当对数据进行操作时，通常有必要以更抽象的形式了解数据，而不仅仅是简单的元素。在我们的音乐库中，你可能想知道一个艺术家有多少不同的歌名，或者你的音乐库中有多少艺术家。您可能还需要知道一个或多个播放列表中正在使用的所有曲目。您可能还想显示曲库(或播放列表)包含的音乐时长。这些都是 reduce 的典型使用案例。(一般来说，所有类型的计数、过滤、分组、分类、统一、连接数据都可以通过应用 reduce 来解决。你甚至可以*滥用*还原为地图数据。)

**请注意:**使用 reduce 对数据进行排序不会有好的结果，而且远远没有达到*的合理水平*。

# `reduce`如何运作

reducer 函数通常是一个函数，它接受累加器(这是一个对象或值，您可以将它看作是先前调用 reducer 函数的结果)和数据元素作为参数，并返回一个新的值/对象:

```
(accumulator, element) => resultValue 
```

Enter fullscreen mode Exit fullscreen mode

在函数式编程的范围内,`reduce`的特别之处在于它有一个状态。**不是**reducer 函数本身(这个函数在理想情况下是无状态的——为了便于测试和在项目中重用),而是缩减数据的过程本身。每个`accumulator`值给出了上一步的中间结果。

减少的概念尤其适用于`Array.prototype.reduce()`(这可能也是它的名字的原因)。reduce 的另一个众所周知的用途是在 *Redux* 状态管理库中。它使用减速器功能，以便使用*动作*和当前状态以可再现的方式改变状态。当前状态作为`accumulator`传递，动作是传递给减速器的`element`参数。

## 回`array.reduce()`

`Array.prototype.reduce()`(或者 JS 中 Array 的每个 Javascript 实例的`array.reduce()`)遍历其实例数组的每个元素，从数组的左侧(`array[0]`)开始，对每个元素调用你的 reducer，对前一个元素调用 reducer 的结果。

```
const array = [ 1, 2, 3, 5 ];
const finalResult = array.reduce((accumulator, element) => {
    console.log("accumulator: ", accumulator);
    console.log("element: ", element);
    const result = accumulator + element;
    console.log("result: ", result);
    return result;
});
console.log("final result: ", finalResult);

// Console output:
//
// > accumulator: 1
// > element: 2
// > intermediate result: 3
// > accumulator: 3
// > element: 3
// > intermediate result: 6
// > accumulator: 6
// > element: 5
// > intermediate result: 11
// > final result: 11
// 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子看起来很好。通过仔细观察，我们可以发现一个基本问题，这使得编写更复杂的 reducer 变得不可行:
传递给 reducer 的`element`参数的第一个值实际上是数组中的第二个值，而实际的第一个数组值在第一次调用时作为`accumulator`值传递给。
**为什么这是个问题？对于更复杂的归约器，这意味着我们必须区分第一次调用和随后的调用，因为第一个累加器值可能与数组元素的类型完全不同(如果不小心处理，会导致执行过程中潜在的`undefined is not a function`异常)。
在你现在立即关闭这篇文章并开始编写可以处理数组元素类型和累加器类型的数据的 reducers 之前:**

**`Array.prototype.reduce()`提供了一种完全避免这个问题的方法:**

它允许您在 reducer 函数旁边指定第二个参数，用作第一个`accumulator`值。设置这个参数将完全避免这个问题:

```
const array = [ 1, 2, 3, 5 ];
const finalResult = array.reduce((accumulator, element) => {
    console.log("accumulator: ", accumulator);
    console.log("element: ", element);
    const result = accumulator + element;
    console.log("result: ", result);
    return result;
}, 0);
console.log("final result: ", finalResult);

// Console output:
//
// > accumulator: 0
// > element: 1
// > intermediate result: 1
// > accumulator: 1
// > element: 2
// > intermediate result: 3
// > accumulator: 3
// > element: 3
// > intermediate result: 6
// > accumulator: 6
// > element: 5
// > intermediate result: 11
// > final result: 11
// 
```

Enter fullscreen mode Exit fullscreen mode

它还允许我们传递不同的累加器类型(但具有相同的接口)，以改变数据被完全归约的方式:

```
const array = [ 1, 2, 3, 5 ];
const finalResult = array.reduce((accumulator, element) => {
    console.log("accumulator: ", accumulator);
    console.log("element: ", element);
    const result = accumulator + element;
    console.log("result: ", result);
    return result;
}, "ConcatedElements: ");
console.log("final result: ", finalResult);

// Console output:
//
// > accumulator: ConcatedElements: 
// > element: 1
// > intermediate result: ConcatedElements: 1
// > accumulator: ConcatedElements: 1
// > element: 2
// > intermediate result: ConcatedElements: 12
// > accumulator: ConcatedElements: 12
// > element: 3
// > intermediate result: ConcatedElements: 123
// > accumulator: ConcatedElements: 123
// > element: 5
// > intermediate result: ConcatedElements: 1235
// > final result: ConcatedElements: 1235
// 
```

Enter fullscreen mode Exit fullscreen mode

使用一个字符串作为第一个`accumulator`值，将连接这些元素，而不是将它们相加。

### 提供不同口味

除了`Array.prototype.reduce()`还有`Array.prototype.reduceRight()`。这基本上是同样的事情，但操作成相反的方向:

```
const array_left = ['1', '2', '3', '4', '5'];
const array_right = ['1', '2', '3', '4', '5'];

const left = array_left.reduce((accumulator, element) => {
    return accumulator + element;
});

const right = array_right.reduceRight((accumulator, element) => {
    return accumulator + element;
});

const equivalentRight = array_left.reverse().reduce((accumulator, element) => {
    return accumulator + element;
});

const equivalentLeft = array_right.reverse().reduceRight((accumulator, element) => {
    return accumulator + element;
});

console.log(left);            
console.log(right);           
console.log(equivalentRight);
console.log(equivalentLeft);

// Console output:
//
// > "12345"
// > "54321"
// > "54321"
// > "12345"
// 
```

Enter fullscreen mode Exit fullscreen mode

就这些了，关于`array.reduce()`的这个简短介绍。也许你知道一些简化器的简便方法(例如，分组数据、统一数据、将数组转换成对象(也许以后用它做 hashmap)或任何其他想法，欢迎你发表在评论中。我将在我的下一篇关于减肥药的文章中包括食谱(带有作者的链接)。
我也感谢任何反馈、批评或纠正。

希望这篇文章有助于将更多的**乐趣**投入到*函数式编程*中；-)