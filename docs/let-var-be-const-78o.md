# 设 var 为常数

> 原文：<https://dev.to/kayis/let-var-be-const-78o>

*[Flickr 上克里斯蒂安·科伦的封面图片](https://www.flickr.com/photos/christiaancolen/)*

在 ES2015 中，JavaScript 获得了一系列新功能，其中两个是允许您声明本地变量的`let`和`const`关键字。

## `var`

用`var`声明的变量将被限定在声明它们的函数范围内。

这意味着，即使你在函数内部的嵌套块*(其他花括号)*中声明它们，它们的*仍然*作用于函数。

例如，如果您在 try 块中声明它，如下所示:

```
 function f(x) {
      try {
        var y = x.toString();
      } catch(e) {
        return "";
      }
      return y;
    } 
```

Enter fullscreen mode Exit fullscreen mode

实际上会这样声明:

```
 function f(x) {
      var y;
      try {
        y = x.toString();
      } catch(e) {
        return "";
      }
      return y;
    } 
```

Enter fullscreen mode Exit fullscreen mode

每一个`var`声明都会被*提升*到函数的顶端。这就是为什么它被认为是一个在函数顶部声明`var` s 的`best practice`,它无论如何都会在那里结束。

如果你想把一个`var`的范围扩大到一个代码块，你必须用一个函数表达式填充或替换它，并在定义后立即调用这个函数。

```
 function f(x) {
      var y;
      if (x >= 0) (function() {
        var z = Math.random() * 10;
        y = x * z;
      })();
      return y;
    } 
```

Enter fullscreen mode Exit fullscreen mode

匿名函数可以访问`f()`的局部变量，所以它可以使用`y`，但是`z`只在匿名函数内部定义，不能在`f()`内部访问。

如你所见，这是相当次优的，但这是许多年来的方式。如果你必须使用 ES5，但由于某种原因不能使用 Babel 这样的编译器，你仍然必须这样做。

## `let`

`let`关键字现在是一种声明变量的方法，这些变量的作用域不是函数，而是块。这意味着任何用花括号*括起来的代码都会限制*这个变量。

```
 function f(x) {
      let y;
      if (x >= 0){
        let z = Math.random() * 10;
        y = x * z;
      } else {
        y = 10;
      }
      return y;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`z`只能在`if`分支内部访问，而`else`分支或`f()`的其余部分无法访问它。

块也可以不用任何语句来使用。

```
 function f(x) {
      let results = [];

      // first calculations
      {
        let a = 10 * 10 * x;
        let b = a * Math.PI;
        results.push(b);
      }

      // second calculations
      {
        let a = 3 + 2 + x;
        let b = a * a;
        let c = a + b;
        results.push(c);
      }

      return results;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这允许将代码结构化，并将变量作用于它们所使用的函数部分。如您所见，这些块可以访问外部作用域变量`results`和`x`。

## `const`

那么`const`关键字是关于什么的呢？你可能在其他语言中见过，比如 C 或 Java。在 JavaScript 中，它声明了一个不可更改的变量，但是*要小心*这只适用于该变量的直接内容。否则范围为`let`。

这个不行:

```
 const a = 10;
    a = 11; 
```

Enter fullscreen mode Exit fullscreen mode

这些在另一方面将:

```
 const a = [1, 2, 3];
    a.push(4);

    const b = {x: 10, y: 20};
    b.z = 30; 
```

Enter fullscreen mode Exit fullscreen mode

为了防止对象(数组也是对象)被更改，您需要`freeze`它们`manually`，这会影响性能。

这将抛出一个错误:

```
 const a = Object.freeze([1, 2, 3]);
    a.push(4); 
```

Enter fullscreen mode Exit fullscreen mode

## 什么时候用什么？！

我尽可能多地使用`const`,如果函数变得更大，找出变量在下降过程中的变化频率和位置通常会变得很麻烦。

有时将`let`与`try/catch`一起使用会有所帮助，因为我必须在`try`中设置一个变量，并且它需要在`try`之后才能被访问。

如今我完全避开`var`。`const`和`let`有相同的作用域，它们唯一的区别是它们的写访问，所以很容易对它们进行互换推理。`var`另一方面，它们的工作方式大不相同，我认为混合在一起只会让事情变得更复杂。