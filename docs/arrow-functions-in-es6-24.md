# ES6 中的箭头功能

> 原文：<https://dev.to/sarah_chima/arrow-functions-in-es6-24>

伴随 ES6 而来的是箭头功能。如果你在 ES6 推出之前就学习了 JavaScript，或者你使用了还没有包含 ES6 特性的资源来学习 JavaScript，那么箭头函数可能会让你有点困惑。这可能是因为它的语法与 ES5 JavaScript 的函数声明和表达式非常不同。不同之处不仅仅在于语法，还包括关键字`this`及其`arguments`的范围。这篇文章可以帮助我们理解所有这些。让我们从它的语法开始。

### 句法

我们将用一个例子来解释这一点。在 ES5 中，如果我们想使用`Array.prototype.map`迭代数组，我们会这样做。

```
 var numbers = [3, 4, 5, 6];

   var threeTimes = numbers.map(function(x) {
       return x * 3;
   });

   console.log (threeTimes);//[9, 12, 15, 18] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要重写 ES6 中的函数，我们将把`(x)`左边的`function`替换成右边的粗箭头`=>`，就像这样:

```
 var threeTimes = numbers.map((x) => {
        return x * 3;
   }); 
```

Enter fullscreen mode Exit fullscreen mode

简单对吗？恭喜我们，我们刚刚创建了一个箭头函数。但好消息是，这甚至可以进一步简化。由于该函数只接收一个参数，我们可以通过去掉`x`两边的括号来进一步简化它。

```
 var threeTimes = numbers.map( x => {
    return x * 3
    }); 
```

Enter fullscreen mode Exit fullscreen mode

酷！！！请注意，如果参数不止一个，它们必须在括号中。我们很快就会看到这样的例子。接下来，我们可以在不损害任何东西的情况下删除箭头后面的花括号:

```
 var threeTimes = numbers.map( x => return x * 3 ); 
```

Enter fullscreen mode Exit fullscreen mode

所以我们现在只有一条函数线。这个函数甚至可以进一步简化，但这会把我们带到另一个特性，它是 arrow 函数语法的一部分——隐式返回。

**隐式返回**

这是什么意思？

也许理解这一点的反面，即显式回报，可以帮助我们更好地理解它。显式返回是指我们编写`return`来告诉函数返回什么，就像上面的例子一样。如果我们只返回一行代码，这在 arrow 函数中是不必要的。隐式返回仅仅意味着如果你返回一行代码，你不必使用关键字`return`。`return`箭头后有一句话是暗示。这很酷，因为大多数 return 语句通常只有一行代码。
所以我们的例子可以写成:

```
 var threeTimes = numbers.map( x =>  x * 3); 
```

Enter fullscreen mode Exit fullscreen mode

看到箭头函数的语法有多简单了吧？

关于箭头函数还有一点需要注意。

箭头函数是匿名的。
这意味着我们不能在 ES6 中这样做:

```
 //ES5
   function sayHello() {
   ... 
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数是一个命名函数。如果出现错误，并且您想知道错误调用的特定函数，这将非常有用。然而，如果我们想让一个函数有一个名字，以便以后调用，我们必须把它存储在一个变量中。下面给出一个例子。

```
 var square = x => x * x;

   square(2); // 4 
```

Enter fullscreen mode Exit fullscreen mode

如果它是一个接收多个参数的函数，它将被写成:

```
 var add = (a,b) => a + b; 
```

Enter fullscreen mode Exit fullscreen mode

如果根本没有争论呢？我们可以简单地把函数写成:

```
 var sayHello = () => "Hello";
   var x = sayHello();// x = "Hello" 
```

Enter fullscreen mode Exit fullscreen mode

我猜你现在已经掌握了语法。我们应该进一步讨论箭头函数的另一个特性——`this`的词法范围。

**箭头函数中`this`的词法范围**

`this`是一个甚至让使用 Javascript 多年的开发人员都感到困惑的关键字。为了解释这一点，我将使用一个例子。我们想创建一个增加秒数的计数器。

```
 //ES5
   function counter() {
      this.seconds = 0;
      this.setInterval(function() {
        this.seconds++;
        console.log(this.seconds);
      }, 1000); 
    }

    counter(); 
```

Enter fullscreen mode Exit fullscreen mode

我们可能期望这能起作用，并且`this.seconds`将在每秒后增加。这是你将得到的。

```
 //NaN
    //NaN
    //NaN
    //NaN 
```

Enter fullscreen mode Exit fullscreen mode

为什么会这样？是因为在 ES5 中，每个函数都绑定了自己的`this`。所以在`setInterval`函数中，`this.seconds`不是指它的父`this.seconds`，而是指窗口的`this.seconds`，它不是一个数字。
为了解决 ES5 中的这个问题，我们要么将父节点的`this`存储在一个变量中，然后像下面这样使用它

```
 //ES5
   function counter() {
      this.seconds = 0;
      var that = this;
      this.setInterval(function() {
        that.seconds++;
        console.log(that.seconds);
      }, 1000); 
    } 
```

Enter fullscreen mode Exit fullscreen mode

或者我们像这样将(this)绑定到`setInterval`函数。

```
 //ES5
   function counter() {
      this.seconds = 0;
      this.setInterval(function() {
        this.seconds++;
        console.log(this.seconds);
      }bind(this), 1000); 
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，你不必经历所有的压力，因为箭头函数不会绑定它们自己的`this`。相反，箭头函数中的`this`总是引用其父函数的`this`。箭头函数继承其父函数的范围。所以上面的例子可以改写为

```
 //ES6
   function counter() {
      this.seconds = 0;
      this.setInterval(() => {
        this.seconds++;
        console.log(this.seconds);
      }, 1000); 
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以`this`值实际上并没有绑定到 arrow 函数。箭头函数中的`this`实际上是从其父函数中获取的。它没有`this`，所以当你使用`this`时，你指的是外部作用域。

**没有参数绑定**

就像在`this`的情况下，箭头函数不绑定它们自己的`arguments`对象。`arguments`在 arrow 函数中是对封闭范围的参数的简单引用。所以我们可以这样做:

```
 var arguments = [1, 2, 3];
    var arr = () => arguments[0];

    arr();//1 
```

Enter fullscreen mode Exit fullscreen mode

它之所以有效，是因为它引用的是其父参数。

这就是你应该了解的关于箭头函数的基本内容。如果想进一步参考， [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 是一个很好的查阅的地方。

有什么问题或补充吗？请留言评论。

感谢您的阅读:)