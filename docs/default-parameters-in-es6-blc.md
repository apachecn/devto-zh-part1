# ES6 中的默认参数

> 原文：<https://dev.to/sarah_chima/default-parameters-in-es6-blc>

ES6 还附带了默认参数。如果没有传递值或传递了 undefined，它允许您为函数参数设置默认值。首先，当没有参数传递给需要参数的函数时会发生什么。我们将定义一个简单的函数，调用它时不需要给它赋值变量。

```
 function add(a,b) {
         return a + b;  
    }
    add() //NaN 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了`NaN`。这就是不带参数调用函数时发生的情况。在 ES6 之前是如何处理的？看看下面的例子。

```
 //ES5
    function add(a,b) {
        var b = (typeof(b) !== "undefined") ? b : 5;
         return a + b; 
    }

    add(4,2) //6
    add(4) //9 
```

Enter fullscreen mode Exit fullscreen mode

所以我们所做的是检查第二个参数`typeof`是否为`undefined`，也就是说没有值传递给它。如果是`true`，则将`b`设置为`5`。所以当我们调用只有一个参数的函数时，结果是`9`。现在我们已经完成了，让我们看看如何在 ES6 中处理这个问题。

```
 function add(a=3, b=5) {
        return a + b; 
    }

    add(4,2) // 6
    add(4) // 9
    add() // 8 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。您只需在初始化参数时给参数赋值。
需要注意的是，参数是从左到右设置的。因此，当您调用该函数时，默认值的覆盖将基于您的输入值的位置发生。例如，在我们的上一个例子中，当一个参数被传递给`add(4)`时，因为`4`被首先传递，所以它被自动假定为`a`。

当一个没有默认值的参数在一个有默认值的参数之后被调用时会发生什么？

```
 function createArray(a = 10, b) {
        return [a,b]; 
    }

    createArray() // [10, undefined]
    createArray(5) //[5, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

因此，这清楚地证明了参数是从左到右设置的，即使后面的参数没有默认值，也会覆盖默认参数。

您也可以将函数设置为默认参数。

```
 function createA() {
        return 10;
    }

    function add(a = createA(), b=5) {
        return a + b; 
    }

    add() // 15 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在执行此操作时，该函数不能是内部函数，因为调用该函数时会计算默认参数。因此，以下内容将不起作用。

```
 function add(a = createA(), b=5) {

        function createA() {
        return 10;
        }
        return a + b; 
    }

    add() // createA is not defined 
```

Enter fullscreen mode Exit fullscreen mode

默认参数也可用于以后的默认参数。也就是在上面的例子中，我们可以将`b`的默认值设置为`a`。不明白吗？我们来举个例子。

```
 function createA() {
        return 5;
    }

    function add(a = createA(), b = a*2, c = b+3) {
        return a + b + c; 
    }

    add() // 28 because 5 + (5*2) + ((5*2) + 3) = 5 + 10 + 13 = 28
    add(2)// 13 because 2 + (2*2) + ((2*2) + 3) = 2 + 4 + 7 = 13
    add(2,3)// 11 because 2 + 3 + (3+3) = 11
    add(2,3,1)//6 
```

Enter fullscreen mode Exit fullscreen mode

我们再做一个只是为了好玩。

```
 function awesome (a = "You", b = a +" are awesome", c = b +" for reading", d = c + " this article", e = d + " to the end" ){

        return e;

    };

    awesome()//You are awesome for reading this article to the end 
```

Enter fullscreen mode Exit fullscreen mode

有什么问题或补充吗？请留言评论。

感谢您的阅读:)