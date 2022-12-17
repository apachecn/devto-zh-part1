# 哪个数组何时起作用？

> 原文：<https://dev.to/andrew565/which-array-function-when>

最近有很多关于“你应该使用 reduce more”或者“你不需要过滤器，使用地图”或者“For？为什么不是 forEach？”

事实是，数组和它们所有的迭代器函数可能会让初学者感到困惑，所以我将试着通过从结尾开始框定问题来为每个人简化事情:你想要返回什么？

## 短版

*   每存在一个东西返回一个东西:`map()`
*   只返回一些现有的东西:`filter()`
*   只返回一个新东西:`reduce()`
*   不要返回任何东西，而是用每个现有的东西做一些事情:`forEach()`

我将简要介绍每一种语法，然后是使用旧的非箭头函数语法和新的箭头函数语法的例子。

# 为每个现有条目返回一个新条目:`map()`

如果你有一个数值数组，你想对数组中的每一项做些什么，并返回一个包含新值的新数组，那么`map()`就是你的朋友。这里有一个简单的函数，它接受一个数组并对每个条目进行加倍:

```
const originalArray = [1, 2, 3];
const newArray = originalArray.map(function(item) {
  return item * 2;
});
console.log(newArray); // -> [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

下面是使用新语法的相同内容:

```
const originalArray = [1, 2, 3];
const newArray = originalArray.map(item => item * 2);
console.log(newArray); // -> [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

注意，使用新的 arrow 语法，我们不必使用 function 关键字、return 关键字或花括号。这是因为箭头函数为像这样的“简单”函数提供了隐式返回。你可以在这里阅读更多关于箭头功能的信息，来自 Wes Bos。

# 返回一个只有部分现有条目的新数组:`filter()`

Filter 可能是最容易理解的数组函数，因为它名副其实。Filter 接受一个值数组，对每个值执行一个函数或比较，然后返回一个只包含通过测试的值的新数组(我们称之为“真值”)。

下面是一个例子，它采用一个数字数组，只返回大于 5 的数字:

```
const originalArray = [1, 9, 4, 2, 42];
const newArray = originalArray.filter(function(item) {
  return item > 5;
});
console.log(newArray); // -> [9, 42] 
```

Enter fullscreen mode Exit fullscreen mode

下面是带箭头功能的滤镜部分:

```
const newArray = originalArray.filter(item => item > 5); 
```

Enter fullscreen mode Exit fullscreen mode

# 只返回一个新东西:`reduce()`

有时你有一组值，你只想从它们中返回一个新的东西。Reduce 接受一个数组，对每一项执行一个函数或比较，然后对所谓的“累加器”做一些事情。这是一个实际上更容易用例子来描述的函数，因为用来描述它的术语和函数本身一样容易混淆！

假设您有一个姓名数组，并且您想要计算姓名“Bob”出现的次数:

```
const originalArray = ["Alice", "Bob", "Charlie", "Bob", "Bob", "Charlie"];
const numberOfBobs = originalArray.reduce(function(accumulator, item) {
  if (item === "Bob") {
    return accumulator + 1;
  } else {
    return accumulator;
  }
}, 0);
console.log(numberOfBobs); // -> 3 
```

Enter fullscreen mode Exit fullscreen mode

再次用箭头:

```
const numberOfBobs = originalArray.reduce((accumulator, item) => {
  if (item === "Bob") {
    return accumulator + 1;
  } else {
    return accumulator;
  }
}, 0); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，arrow 函数这次没有为我们节省太多的输入，因为我们必须向函数提供两个参数，然后在返回之前有逻辑，所以我们仍然需要花括号。

reduce 函数末尾的 0 是我们开始使用累加器的值，如果我们遇到的值是“Bob ”,则加 1，否则我们返回当前的累加器。如果你没有返回任何东西，那么下一次函数运行时，累加器将会是`undefined`。

# 对每个数组值做一些事情，但不返回任何东西:`forEach()`

有时你会有一个数组值，你想做一些事情，但不需要跟踪每个函数调用的返回是什么。这就是`forEach()`的作用。

```
const originalArray = [1, 2, 3];
originalArray.forEach(function(item) {
  doSomething(item);
}); 
```

Enter fullscreen mode Exit fullscreen mode

并且再次用箭头:

```
originalArray.forEach( item => doSomething(item); ); 
```

Enter fullscreen mode Exit fullscreen mode

# 期末笔记

简单又甜蜜。这些是我能为每个函数想出的最简单的用例，并尽可能使它容易理解你什么时候应该使用它们。您可以使用这些函数完成大量的任务，并且这些函数中的每一个都有一个“高级”表单，它也可以为您提供当前索引:

```
arr.map((item, index) => {})
arr.filter((item, index) => {})
arr.reduce((accumulator, item, index) => {})
arr.forEach((item, index) => {}) 
```

Enter fullscreen mode Exit fullscreen mode

需要就用吧！