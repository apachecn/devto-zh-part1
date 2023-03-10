# 固化和局部应用

> 原文：<https://dev.to/gnclmorais/currying-and-partial-application-3f1c>

我时常发现自己将 JavaScript 中的一个函数绑定到几个值上，并且想知道*我现在记不起来了…这是部分应用还是奉承？*。我知道这些概念是相关的，但我一直在混淆它们，发现它们很难区分。于是我决定去读一读，试着用自己的话来表达，这样我就可以永远地把它们记在我的记忆里了。

## 局部应用

简单地说，**部分应用**是接受 N 个参数并将值绑定到一个或多个参数的函数的行为。这将返回一个新的函数(其 [arity](https://en.wikipedia.org/wiki/Arity) 小于 N ),该函数接受剩余的非有界参数。让我们看一些例子。

```
function sumCoordinates(x, y, z) {
    return x + y + z;
}
console.log(sumCoordinates.length); // 3, meaning it accepts three arguments

var sumYandZ = sumCoordinates.bind(null, 1);
sumYandZ.length // 2, meaning it accepts two arguments

sumCoordinates(1, 2, 3) === sumYandZ(2, 3); // true

// A more useful example
const times = (a, b) => a * b;
const double = times.bind(null, 2);
double(10); // 20
double(21); // 42 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，通过调用`sumCoordinates.bind(null, 1)`，我们部分地应用了函数`sumCoordinates`，将它的第一个参数绑定到了`1`。`sumYandZ`只不过是一个新函数，用预先绑定的第一个值调用`sumCoordinates`。

部分应用一个功能通常包括两个步骤。第一步是将它绑定到我们想要的预设参数的数量上，第二步是在剩余的参数上调用它(或者一个都没有)。

## 逢迎

现在，currying 是一种与局部应用略有不同的野兽。一个接受 N 个参数的函数将返回一个函数，该函数代表一个接受单个参数的 N 个函数的链。我知道这听起来很奇怪，所以让我用一个例子来更好地解释它。

```
// JavaScript doesn’t have native currying,
// so we need a helper for that
function curry(fn) {
  // Save the number of required arguments of the original function
  const fnArity = fn.length;

  return (function currier() {
    // Create a cache for arguments, were they will be stored
    let argCache = Array.prototype.slice.call(arguments);

    return function () {
      // Get the next arguments and add them to the argument cache
      let argsSoFar = argCache.concat(...arguments);
      // If we are still short on arguments, keep returning a curried function;
      // Otherwise, apply the original function to all the arguments so far
      let next = argsSoFar.length < fnArity ? currier : fn;
      return next.apply(null, argsSoFar);
    };
  }());
}

function sumCoordinates(x, y, z) {
    return x + y + z;
}

var curriedSum = curry(sumCoordinates);
curriedSum.length // 0, as it relies on the `arguments` object

typeof curriedSum(1) // "function"
typeof curriedSum(1)(2) // "function"
typeof curriedSum(1, 2) // "function"
typeof curriedSum(1)(2)(3) // "number"
typeof curriedSum(1, 2)(3) // "number"
6 === curriedSum(1)(2)(3) // true
6 === curriedSum(1, 2)(3) // true
6 === curriedSum(1, 2, 3) // true 
```

Enter fullscreen mode Exit fullscreen mode

一个简化函数最多可以有`N + 1`个步骤，其中 N 是原始简化函数的参数个数。第一步是 currying 函数，接下来的步骤取决于一次传递给函数多少个参数。正如你在上面看到的，`curriedSum(1, 2, 3)`将给出与`curriedSum(1)(2)(3)`相同的结果，因为两个函数都有必要数量的参数来实现最初的 curried 函数。