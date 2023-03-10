# 5 岁儿童的高阶函数

> 原文：<https://dev.to/kayis/higher-order-functions-for-5-year-olds>

高阶函数是函数式编程的主要思想之一，但许多开发人员不理解它们，这也花了我几个月的时间，所以我将尝试解释它们。希望我的解释会简单到一个 5 岁的孩子都能理解，我对此表示怀疑，哈哈。

首先，让我们谈谈函数。它们是你可以放入东西和从中取出其他东西的东西，在它们之间，它们对你放入的东西做一些事情。

你放进去的东西叫做参数，出来的东西叫做返回值。在许多编程语言中，函数只能有一个返回值，有些甚至只有一个参数。

**是什么让一个正则函数成为高阶函数？**

如果您有一个函数，它接受一个或多个参数**和/或**返回一个或多个函数作为它们的返回值，那么您有一个高阶函数。

不多不少。

*5 年模式关闭*

## 额外 1:糖纸

通常，与函数一起工作的其他函数被称为*包装器*。而包装器的工作原理通常就像你用它们*包装*的东西。高阶函数更一般。

```
 function timesTen(x) { return x * 10; }

    // a wrapper
    // works always with the same function it wraps
    function timesTenPlusTen(x) { return timesTen(x) + 10; }
    timesTenPlusTen(10);

    // a higher order function
    // works with the function it takes as an argument
    function doXPlusTen(f, x) { return f(x) + 10; }
    doXPlusTen(timesTen, x); 
```

Enter fullscreen mode Exit fullscreen mode

## 额外 2:一个自变量函数

我谈到了函数只能接受一个参数的语言，那么这是如何工作的呢？

他们可以返回一个函数，这些他们创建的函数可以访问他们的父函数的参数。

```
function times(x) {
  return function(y) {
    return x * y;
  };
}
// in JavaScript it would be used like that:
let f = times(10); // returns the second function and saves it in f
f(10); // executes the second function that has access to x AND y

// in short
times(10)(10); 
```

Enter fullscreen mode Exit fullscreen mode

这也叫做 currying，通常这些语言都有简短的函数创建语法。

```
function f(x) { return x * x; }
// works like
let f = x => x * x;

// so our example from above could be written as
let times = x => y => x * y;

// still works as before
times(10)(10); 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么？

正如您所看到的，由于一个参数的规则，一些语言没有这个概念甚至无法工作，但是我每天遇到的最基本的用法是让您添加其他函数作为事件侦听器的函数。所以你可能从 JavaScript 等语言中知道的`addEventListener`函数是高阶函数，因为它们的参数之一必须是函数。

```
function doSomething() { alert("Loaded!"); }
window.addEventListener('load', doSomething); 
```

Enter fullscreen mode Exit fullscreen mode