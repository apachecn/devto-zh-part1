# Javascript 范围如何在 JavaScript 中创建范围

> 原文：<https://dev.to/ycmjason/how-to-create-range-in-javascript-539i>

`range`是一个函数，它基本上接受一个起始索引和结束索引，然后返回一个从头到尾的所有整数的列表。

最明显的方法是使用 for 循环。

```
function range(start, end) {
    var ans = [];
    for (let i = start; i <= end; i++) {
        ans.push(i);
    }
    return ans;
} 
```

Enter fullscreen mode Exit fullscreen mode

作为 FP 的爱好者，我们来想一个递归的解决方案。所以基本情况显然是当开始和结束相同时，答案就是`[start]`。

```
function range(start, end) {
    if(start === end) return [start];
    // recursive case
} 
```

Enter fullscreen mode Exit fullscreen mode

现在来个信念的飞跃，假设`range(start, end)`会*正常工作*。那我们怎么解决问题`range(start, end)`？简单！就做`[start, ...range(start + 1, end)]`。

所以把两者结合起来，我们得到

```
function range(start, end) {
    if(start === end) return [start];
    return [start, ...range(start + 1, end)];
} 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这比 for 循环的解决方案要优雅得多。但是我们甚至可以更进一步，如果我们使用`new Array(n)`创建一个有 n 个元素的数组。

如果我们有一个 n 元素列表，我们可以通过将每个元素映射到它的索引来构建一个范围，即`arr.map((_, i) => i)`。

但是根据[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/map # Description](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#Description)，`map`不会调用未赋值的元素。这意味着我们需要在映射前初始化`new Array(n)`。一个标准的技术是使用`fill`。最终结果如下。

```
function range(start, end) {
    return (new Array(end - start + 1)).fill(undefined).map((_, i) => i + start);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以利用 [`Array.from`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 来创建范围:

```
function range(start, end) {
  return Array.from({ length: end - start + 1 }, (_, i) => i)
} 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Step 提到处理大范围时的效率，这实质上构建了一个巨大的数组。通过使用发电机，我们可以有一个更有效的方法来做到这一点。

```
function* range(start, end) {
    for (let i = start; i <= end; i++) {
        yield i;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在一个`for...of`循环中使用这个生成器(这将非常有效),或者使用一个数组展开来检索所有值(注意，这实际上构建了一个数组，它与非生成器方法基本相同。)

```
for (i of range(1, 5)) {
    console.log(i);
}
/* Output
 * 1 2 3 4 5 */

[...range(1, 5)] // [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

因为我总是试图避免 for 循环，我们也可以如下递归地定义生成器。

```
function* range(start, end) {
    yield start;
    if (start === end) return;
    yield* range(start + 1, end);
} 
```

Enter fullscreen mode Exit fullscreen mode

你能想出一些更酷的方法来达到这个目的吗？