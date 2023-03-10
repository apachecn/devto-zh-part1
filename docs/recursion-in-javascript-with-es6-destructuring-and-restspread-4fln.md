# ES6 JavaScript 中的递归、析构和 rest/spread

> 原文：<https://dev.to/hugo__df/recursion-in-javascript-with-es6-destructuring-and-restspread-4fln>

最新的 JavaScript ECMA 标准(ECMAScript 6)通过使用函数构造和新的操作符鼓励更具声明性的风格，使 JavaScript 更具可读性。

## 解构

我最喜欢的 ES6 功能之一是**析构**。它允许你通过使用**结构**从一个变量提取数据到另一个变量。对于数组，这意味着例如:

```
var [first, second] = [1, 2, 3, 4];
// first: 1
// second: 2 
```

Enter fullscreen mode Exit fullscreen mode

你还可以做更多的事情，比如跳过运算右边数组的一些成员。

```
var [first, , third, fourth] = [1, 2, 3, 4];
// first: 1
// third: 3
// fourth: 4 
```

Enter fullscreen mode Exit fullscreen mode

这实际上很容易移植到等效的 ES5

```
var arr = [1, 2, 3, 4];
var first = arr[0];
var second = arr[1];
// etc ... 
```

Enter fullscreen mode Exit fullscreen mode

## 休息

这就是 ES6 特性变得更有趣的地方。通过析构，我们还可以分配数组的其余部分。我们用表示**休息**...符号。

```
var [first, ...notFirst] = [1, 2, 3, 4];
// first: 1
// notFirst: [ 2, 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

命名约定产生的代码更类似于下面的代码:

```
var [first, second, ...rest] = [1, 2, 3, 4];
// first: 1
// second: 2
// rest: [ 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

rest 操作符有一些有趣的性质:

```
var [first, ...rest] = [1];
// first: 1
// rest: [] 
```

Enter fullscreen mode Exit fullscreen mode

它总是返回一个数组。这意味着即使在防御性的 JavaScript 领域，也可以做类似 check 这样的事情。无防护的休息时间。

ES5(及以下版本)中的等效方法是使用 Array.slice 函数。

```
var arr = [1, 2, 3, 4];
var first = arr[0];
var rest = arr.slice(1);
// first: 1
// rest: [ 2, 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

这里要注意两件事:

*   ES5 版本更加冗长

*   ES5 版本更具命令性，我们告诉 JavaScript **如何**做某事，而不是告诉它**我们想要什么**。

现在我也觉得结构匹配版(带 rest)可读性更强。

## 参数析构

我们可以在函数定义的参数上使用析构:

```
function something([first, ...rest]) {
  return {
    first: first,
    rest: rest
  };
}
var result = something([1, 2, 3]);
// result: { first: 1, rest: [ 2,3 ] } 
```

Enter fullscreen mode Exit fullscreen mode

等效 ES5:

```
function something(arr) {
  var first = arr[0];
  var rest = arr.slice(1);
  return {
    first: first,
    rest: rest
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，它更冗长，也更有命令性。

## 传播开来

Spread 使用与 rest 相同的符号:....它的功能完全不同。

```
var arr = [1, 2, 3];
var newArr = [...arr];
// newArr: [ 1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

ES5 等价:

```
var arr = [1, 2, 3];
var newArr = [].concat(arr); 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，数组的内容是**复制的**。所以 newArr 不是对 Arr 的引用。

我们还可以做一些事情，比如追加或者预先放置一个数组。

```
var arr = [1, 2, 3];

var withPrepend = [...arr, 3, 2, 1];
var withAppend = [3, 2, 1, ...arr];
// withPrepend: [ 1, 2, 3, 3, 2, 1]
// withAppend: [ 3, 2, 1, 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

## 函数式编程:列出&递归

在函数式编程中，当我们在列表上递归运行函数时，我们喜欢将列表建模为头部**和尾部**的列表。****

头部是列表的第一个元素，尾部是列表减去头部后的列表。

```
arr = [1, 2, 3];
// head(arr): 1
// tail(arr): [ 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，我们可以通过用**析构**和**剩余:**
适当地命名变量来做到这一点

```
var [head, ...tail] = [1, 2, 3];
// head: 1
// tail: [ 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用 ES6:
轻松实现 head 和 tail 函数

```
function head([head, ...tail]) {
  return head;
}
function tail([head, ...tail]) {
  return tail;
}
// or with arrow function syntax
var head = ([head, ...tail]) => head;
var tail = ([head, ...tail]) => tail; 
```

Enter fullscreen mode Exit fullscreen mode

### (尾)递归

我们可以使用**参数析构****和**递归**来实现对数组(或者在函数式编程中倾向于称为列表)进行操作的函数。

例如，map 可以通过以下方式实现:

*Map 是一个函数，它接受一个列表和一个函数，并返回一个列表，该列表包含函数应用于列表中每个元素的结果。*

```
function map([head, ...tail], fn) {
  if (head === undefined && !tail.length) return [];
  if (tail.length === 0) {
    return [fn(head)];
  }
  return [fn(head)].concat(map(tail, fn));
} 
```

Enter fullscreen mode Exit fullscreen mode

`tail.length === 0`检查是否还有尾部可以递归。否则，递归在此停止。

就内存使用和速度而言，这不一定是最有效的 map 版本，但它是 ES6 的一个很好的例子。

我们可以通过用 spread 操作符替换 concat 并使用带有三元操作符的单一 return 语句来进一步简化它。

### 非常 ES6 地图

我们的 ES6 递归/析构映射可以简化为:

```
function map([head, ...tail], fn) {
  if (head === undefined && !tail.length) return [];
  return tail.length ? [fn(head), ...map(tail, fn)] : [fn(head)];
} 
```

Enter fullscreen mode Exit fullscreen mode

或者如果我们想滥用 ES6，让自己忘记我们实际上是在做 JavaScript:

```
const map = ([head, ...tail], fn) =>
  head !== undefined && tail.length
    ? tail.length
      ? [fn(head), ...map(tail, fn)]
      : [fn(head)]
    : []; 
```

Enter fullscreen mode Exit fullscreen mode

ES5 当量

```
function map(arr, fn) {
  var head = arr[0];
  var tail = arr.slice(1);
  if (head === undefined && tail.length === 0) return [];
  if (tail.length === 0) {
    return [fn(head)];
  }
  return [].concat(fn(head), map(tail, fn));
} 
```

Enter fullscreen mode Exit fullscreen mode

所有的特性加起来，虽然 ES6 中的递归映射本质上是一行程序，但在 ES5 中，它是一个笨重、冗长、难以阅读的函数。

### 重新实现列表操作功能

现在，您可以尝试使用上述技术重新实现 filter、reduce 和 join。

折下解:)。

ES6 允许我们用函数式风格更简洁有效地编写代码。

### ES6 中带 rest/spread 和析构的递归列表操作

使用 ES6、析构和递归的过滤器实现:

```
function filter([head, ...tail], fn) {
  const newHead = fn(head) ? [head] : [];
  return tail.length ? [...newHead, ...filter(tail, fn)] : newHead;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 ES6、析构和递归减少实现:

```
function reduce([head, ...tail], fn, initial) {
  if (head === undefined && tail.length === 0) return initial;
  if (!initial) {
    const [newHead, ...newTail] = tail;
    return reduce(newTail, fn, fn(head, newHead));
  }
  return tail.length
    ? reduce(tail, fn, fn(initial, head))
    : [fn(initial, head)];
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 ES6、析构和递归连接实现:

```
function join([head, ...tail], separator = ",") {
  if (head === undefined && !tail.length) return "";
  return tail.length ? head + separator + join(tail, separator) : head;
} 
```

Enter fullscreen mode Exit fullscreen mode