# 基本 JavaScript 数组函数

> 原文：<https://dev.to/jackmarchant/essential-javascript-array-functions-169e>

JavaScript 中到处都是数组，但是很容易把它们弄得一团糟，几乎无法测试。这里有一个有用函数的快速总结，您可以在每个数组上使用，而不需要改变原来的数组。

[![](img/89e854c37cc154d2729c330e3640a89c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cUij-trB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApVNYww6FHRwpqxeIwbCfFw.jpeg) 

<figcaption>数组在 JavaScript 中无处不在——为什么不学习一下如何不把它们弄得一团糟呢？</figcaption>

### **地图**

[*Array.map*](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 函数是数组中最常用的函数之一。它将一个函数作为参数，并在每次迭代中调用该函数，同时循环调用它的数组。

```
// Array.prototype.map

const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n \* 2);

console.log(doubled); // [2, 4, 6, 8, 10]
console.log(numbers); // [1, 2, 3, 4, 5] 
```

### **滤镜**

[*Array.filter*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 函数提供了一种从数组中移除那些没有通过提供给 filter 函数的函数中的测试的项目的方法。它遍历数组中的每一项，如果函数的计算结果为(返回)true，则返回该项，否则从筛选后的数组中排除该项。

```
// Array.prototype.filter

const numbers = [1, 2, 3, 4, 5];

const filtered = numbers.filter(n => n > 2);

console.log(filtered); // [3, 4, 5];
console.log(numbers); // [1, 2, 3, 4, 5]; 
```

### 串联

[*Array.concat*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat) 函数(类似于 [*String.concat*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/concat) )允许您在不修改原始数组的情况下向数组中添加新项。通常，在可以使用 *Array.push* 的地方，几乎可以肯定可以使用 *Array.concat* 来代替。您可以传递要添加到数组中的单个项目，也可以传递另一个完整的数组，然后将该数组加入到现有的数组中。

```
// Array.prototype.concat

const numbers = [1, 2];

const concatenatedNumber = numbers.concat(3);

const concatenated = concatenatedNumber.concat([4, 5]);

console.log(concatenated); // [1, 2, 3, 4, 5]; 
```

### 切片

[*Array.slice*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) 函数为您提供了一种从数组中移除项目的方法，而无需修改原来的数组。通常与 splice 混淆，但 splice 会使原始数组发生变异。

```
// Array.prototype.slice

const numbers = [1, 2, 3, 4, 5];

const selectedNumbers = numbers.slice(0, 2);

console.log(selectedNumbers); // [1, 2]
console.log(numbers); // [1, 2, 3, 4, 5] 
```

### 减少(或减少权利)

[*Array.reduce*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 和[*array . reduce right*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)函数的目的是获取一个数组并将其转换为单个值。reducer 函数将从左到右迭代数组中的项目，而*array . reducer right*将从右边开始，然后向左移动。

在每次迭代中，谓词函数会传入前一个值(运行上一次迭代的结果，或者初始值)，以及数组中的当前项。

这看起来有点复杂和混乱，但是理解 reduce 是理解像 [Redux 这样的库的关键，Redux 以一种非常酷的方式使用了 reducer 的思想](http://redux.js.org/docs/basics/Reducers.html)！

```
// Array.prototype.reduce || Array.prototype.reduceRight

const numbers = [1, 2, 3, 4, 5];

const sum = numbers.reduce((prev, curr) => prev + curr, 0);

console.log(sum); 15 
```

在上面的例子中，初始值是 0，在每次迭代中，它将取 0 或先前的结果，并将其添加到当前项中。这个 reduce 函数的结果将是一个值— 15。

你可以认为减压器比我在这里介绍的要强大得多，事实也确实如此

### 为什么我们试图不改变任何数组？我就不能打电话吗？按或。拼接？

使用我上面概述的函数并将结果赋给一个新的变量，而不是改变原始数组，这意味着您可以在代码中看到一个更加显式的流程。这使得在其他函数中使用这些函数变得更加容易，尤其是当您正在编写测试并期望得到可预测的结果时。

在许多编程语言中有一个通用的经验法则，但在 JavaScript 中可能最普遍的是

> 仅仅因为你能，并不意味着你应该..

可以用。比如说 push，但是如果在 push 发生之后，你想在你的代码中引用这个数组呢？您需要在原始数组变异之前创建另一个对它的引用，或者重构。推动使用非变异方法，然后编写新代码——或者您可以从一开始就使用纯函数！