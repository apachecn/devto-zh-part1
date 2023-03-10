# 不要支付循环税

> 原文：<https://dev.to/danhomola/dont-pay-the-for-looptax>

*注:此[帖](https://medium.com/@DanHomola/dont-pay-the-for-loop-tax-802d381d1cd4)原载于[我的媒体简介](https://medium.com/@DanHomola)T5*

有一次，当我在工作中对一个 TypeScript 项目进行代码审查时，我遇到了几个我的一个同事使用 for 循环的实例，尽管这并不是必要的(例如，有一个可读性更好的声明性等效物可用)。在一个笑话中，我说我们应该对每一个不必要使用的循环征收“循环税”。然而，这让我思考，为什么这么多人倾向于使用更长、更容易出错的循环解决方案，我得出了以下结论:我曾经学习/阅读的几乎每一门命令式编程语言课程/书籍(无论是高中的 Pascal 和 C#，还是大学的 C/C++和 Wolfram Mathematica ),都包含这样的部分

> 这些是可用的循环，这是你如何写它们，这是你如何使用它们来解决这些基本问题。

这里有重要的一点要注意:他们只教如何写一个循环，但是很少解释为什么你需要一个循环(或者有时甚至更糟，他们说基于循环的解决方案是最好的)。为了将来的参考，我决定写一本“循环经常被使用的主要情况以及如何替换它们的食谱”。所有的例子都将使用 JavaScript 编写，因为它非常流行，但是这些例子背后的基本原理也可以在许多其他语言中使用。

## #1:我需要遍历一个数组，得到一个单一的值作为结果

我们从最简单的问题开始:

> 给定一个数字数组，返回其元素之和。

```
const sum = (array) => {
    let result = 0;
    for (let i = 0; i < array.length; i++) {
        result += array[i];
    }
    return result;
}

const numbers = [5, 25, 8, 18];
console.log(sum(numbers)); // logs 56 
```

如果你和我一样参加过类似的课程，你肯定认识这个代码。创建一个临时变量，用零初始化它，并使用 for 循环迭代数组，返回变量的最终值。尽管存在一些问题:对于像数组求和这样简单的事情，7 行代码似乎太多了。你必须自己处理迭代的边界。换句话说，您必须知道从零开始(在 JavaScript 中，许多其他语言都有基于 1 的数组——例如 Wolfram Mathematica ),到严格小于数组长度(不小于或等于)的 I 结束。这很容易出错，尤其是当你同时用多种语言工作的时候。

```
const sum = (array) => array.reduce(
  (total, current) => total + current,
  0);

const numbers = [5, 25, 8, 18];
console.log(sum(numbers)); // logs 56 
```

补救这两个问题的解决方案是使用 [`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 函数(在其他语言中也称为`fold`或`aggregate`)。在单个表达式中，我们迭代每个数组元素，将它们加在一起(声明总和的默认值和初始值为零)。注意这里没有提到迭代边界，它只是保证从头到尾遍历所有元素。

## 我需要从一个给定的数组创建一个新的数组，并转换所有的元素

这是另一个常见问题，让我们用这个例子来说明:

> 给定价格数组，返回一个新数组，价格比数组低 *n* %。

```
const discount = (originalPrices, discountAmount) => {
    const multiplier = 1 - discountAmount;
    // we must clone the array
    let result = new Array(originalPrices);
    for (let i = 0; i < originalPrices.length; i++) {
        result[i] = originalPrices[i] * multiplier;
    }
    return result;
}

const prices = [5, 25, 8, 18];
console.log(discount(prices, 0.2)); //logs [ 4, 20, 6.4, 14.4 ] 
```

基于循环的方法非常类似于 sum 代码。但是还有一个额外的问题:为了不破坏输入数组，我们必须首先克隆它，然后转换新数组中的值。这很容易被忘记，在应用程序中引入了潜在的不希望的副作用。

```
const discount = (originalPrices, discountAmount) => {
    const multiplier = 1 - discountAmount;
    return originalPrices.map(price => price * multiplier);
}

const prices = [5, 25, 8, 18];
console.log(discount(prices, 0.2)); // logs [ 4, 20, 6.4, 14.4 ] 
```

使用 [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Map) 功能可以完全避免克隆问题。对于给定的数组，它返回一个新的数组，其中每个元素都是使用提供的函数转换的原始数组中的相应元素(在我们的示例中是乘以折扣乘数)。

## #3:我需要从 *m* 到 *n* 的数字

使用循环的另一种常见情况是生成线性范围作为进一步变换的输入。一个经典的例子是:

> 返回前 *n* 个方块的数组

```
const squaresBad = (n) => {
    let result = [];
    for (let i = 1; i <= n; i++) {
        result.push(i * i);
    }
    return result;
}

const squares = (n) => {
    let result = new Array(n);
    for (let i = 1; i <= n; i++) {
        result[i - 1] = i * i;
    }
    return result;
}

console.log(squaresBad(5)); // logs [ 1, 4, 9, 16, 25 ]
console.log(squares(5)); // logs [ 1, 4, 9, 16, 25 ] 
```

这是一个在使用循环时很难解决的问题。第一种简单的解决方案存在这样的问题，即每次迭代都要向数组中加入一个新元素。这将扩展阵列，并可能导致它在内存中重新分配缓慢([基准](https://jsperf.com/array-assign-into-new-array-vs-push))。
第二种方法预先实例化了正确大小的数组，避免了这个问题，但是我们很容易在分配当前值时出错(参见第二个 for 循环中的`result[i – 1]`表达式)。

```
 const range = require("lodash.range")
const squaresLodash = (n) => range(1, n + 1).map(
    (n) => n * n);

const squares = (n) => [...Array(n).keys()].map(
    (n) => (n + 1) * (n + 1));

console.log(squaresLodash(5)); // logs [ 1, 4, 9, 16, 25 ]
console.log(squares(5)); // logs [ 1, 4, 9, 16, 25 ] 
```

虽然在 JavaScript 中没有生成整数范围的原生方法，但有两种方法可以用`map`以更具声明性的方式解决这个问题:使用`lodash.range`函数，或者聪明的 ES2015 语法技巧([来源](https://stackoverflow.com/questions/3895478/does-javascript-have-a-method-like-range-to-generate-an-array-based-on-suppl/10050831#10050831))。

## #4:我需要做一些有副作用的事情 *n* 次

我想在这里讨论循环的最后一个用例是多次调用有副作用的方法。正如埃德格·迪克斯特拉的名言所说:

> 两个或更多，使用

说明这种情况的最简单的例子是:

> Console.log 字符串“Hello world *n* 次

在我看来，这是 JavaScript 中唯一合理的循环用例(不包括无限循环),因为这是最简洁、最高效的方式(至少在大多数环境中使用[尾调用优化](http://2ality.com/2015/06/tail-call-optimization.html)之前是如此)。
然而，我强烈建议将它抽象成一个帮助函数，将循环限制在一个地方。

```
const doNTimesLoop = (n, f) => {
    for (let i = 1; i <= n; i++) {
        f(i);
    }
}

const doNTimesRec = (n, f) => {
    const body = (m) => {
        if (m > n) return;
        f(m);
        return body(m + 1);
    }

    return body(1);
}

//both log "Hello world" five times
doNTimesLoop(5, x => console.log("Hello world"));
doNTimesRec(5, x => console.log("Hello world")); 
```

正如我们在示例中看到的(都是用从 1 到 *n* 的数字调用提供的函数)，迭代版本更短，编写起来更简单。此外，“无循环版本”会在没有尾部调用优化的环境中导致堆栈溢出。

## 结论

在四种基本情况下，我们描述了如何使用声明式风格来替换循环，从而使我们的代码更短，更不容易出错。你使用循环吗？你不同意这些解决方案吗？请评论！