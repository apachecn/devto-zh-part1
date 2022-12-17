# Var，let 和 const-有什么区别？

> 原文：<https://dev.to/sarah_chima/var-let-and-const--whats-the-difference-69e>

ES2015 (ES6)带来了许多闪亮的新功能，因为现在是 2017 年，所以假设许多 JavaScript 开发人员已经熟悉并开始使用这些功能。虽然这种假设可能是真的，但对某些人来说，其中一些特征仍然是个谜。

ES6 的一个特性是增加了`let`和`const`，它们可以用于变量声明。现在的问题是，它们与我们一直在使用的`var`有何不同？如果你还不清楚这一点，这篇文章是给你的。

本文将对`var`、`let`和`const`的范围、用途和吊装进行讨论。当你阅读时，记下我将要指出的它们之间的区别。

### 增值转销公司

在 ES6 出现之前，`var`宣言统治为王。但是用`var`声明的变量存在一些问题。这就是为什么有必要出现声明变量的新方法。不过，在我们讨论其中一个问题之前，让我们先多了解一下`var`。

**`var`范围**

**范围**本质上是指这些变量可以使用的地方。声明是全局作用域或函数/局部作用域。当一个`var`变量在函数外声明时，它是全局作用域的。这意味着任何在功能块外用`var`声明的变量都可以在整个窗口中使用。`var`当在函数中声明时，函数是否有作用域。这意味着它是可用的，并且只能在该函数中访问。为了进一步理解，请看下面的例子。

```
 var greeter = "hey hi";

    function newFunction() {
        var hello = "hello";
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，`greeter`是全局范围的，因为它存在于函数之外，而`hello`是函数范围的。所以我们不能在函数之外访问变量`hello`。所以如果我们这样做:

```
 var tester = "hey hi";

    function newFunction() {
        var hello = "hello";
    }
    console.log(hello); // error: hello is not defined 
```

Enter fullscreen mode Exit fullscreen mode

我们将得到一个错误，这是由于`hello`在函数之外不可用。

**`var`变量可以重新声明和更新**

这意味着我们可以在相同的范围内这样做，并且不会出错。

```
 var greeter = "hey hi";
    var greeter = "say Hello instead"; 
```

Enter fullscreen mode Exit fullscreen mode

而这也

```
 var greeter = "hey hi";
    greeter = "say Hello instead"; 
```

Enter fullscreen mode Exit fullscreen mode

**`var`**吊装

提升是一种 JavaScript 机制，在代码执行之前，变量和函数声明被移动到它们作用域的顶部。这意味着如果我们这样做:

```
 console.log (greeter);
    var greeter = "say hello" 
```

Enter fullscreen mode Exit fullscreen mode

它被解释为这个

```
 var greeter;
    console.log(greeter); //greeter is undefined
    greeter = "say hello" 
```

Enter fullscreen mode Exit fullscreen mode

所以`var`变量被提升到其作用域的顶部，并用一个未定义的值初始化。

**问题同`var`**

伴随着`var`而来的还有一个弱点。我将用下面的例子来解释这一点。

```
 var greeter = "hey hi";
    var times = 4;

    if (times > 3) {
        var greeter = "say Hello instead"; 
    }

    console.log(greeter) //"say Hello instead" 
```

Enter fullscreen mode Exit fullscreen mode

所以，由于`times > 3`返回 true，`greeter`被重新定义为`"say Hello instead"`。虽然如果你有意想要重新定义`greeter`这不是问题，但是当你没有意识到变量`greeter`之前已经被定义了，这就成了问题。如果你在代码的其他部分使用了`greeter`，你可能会对你得到的输出感到惊讶。这可能会在您的代码中导致许多错误。这就是为什么`let`和`const`是必要的。

### 让

现在变量声明首选`let`。这并不奇怪，因为它是对`var`声明的改进。它还解决了上一个小标题中提出的问题。让我们考虑一下为什么会这样。

**`let`是块作用域**

块是由{}限定的代码块。块住在花括号里。花括号内的任何内容都是块。所以在带有`let`的块中声明的变量只能在该块中使用。我举个例子来解释一下。

```
 let greeting = "say Hi";
   let times = 4;

   if (times > 3) {
        let hello = "say Hello instead";
        console.log(hello);//"say Hello instead"
    }
   console.log(hello) // hello is not defined 
```

Enter fullscreen mode Exit fullscreen mode

我们看到在它的块(定义它的花括号)之外使用`hello`会返回一个错误。这是因为`let`变量是块范围的。

**`let`可以更新，但不能重新申报。**

就像`var`一样，用`let`声明的变量可以在其作用域内更新。与`var`不同的是，`let`变量不能在其作用域内重新声明。因此，虽然这将工作，

```
 let greeting = "say Hi";
    greeting = "say Hello instead"; 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个错误。

```
 let greeting = "say Hi";
    let greeting = "say Hello instead";//error: Identifier 'greeting' has already been declared 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果在不同的作用域中定义了同一个变量，则不会出现错误。

```
 let greeting = "say Hi";
    if (true) {
        let greeting = "say Hello instead";
        console.log(greeting);//"say Hello instead"
    }
    console.log(greeting);//"say Hi" 
```

Enter fullscreen mode Exit fullscreen mode

为什么没有错误？这是因为两个实例被视为不同的变量，因为它们具有不同的范围。

这个事实使得`let`比`var`更好的选择。当使用`let`时，你不必担心你以前是否为变量取过名字，因为变量只存在于它的作用域内。此外，由于一个变量不能在一个作用域内声明多次，那么前面讨论的用`var`出现的问题就不会出现。

**`let`**
的吊装与`var`、`let`的申报一样，都是吊装到顶部。与初始化为`undefined`的`var`不同，`let`关键字没有初始化。所以如果你试图在声明前使用一个`let`变量，你会得到一个`Reference Error`。

### 常数

用`const`声明的变量保持常量值。`const`声明与`let`声明有一些相似之处。

**`const`声明是块范围的**

像`let`声明一样，`const`声明只能在声明它的块中被访问。

**`const`不能更新或重新声明**

这意味着用`const`声明的变量的值在其作用域内保持不变。它不能更新或重新声明。所以如果我们用`const`声明一个变量，我们也不能这样做

```
 const greeting = "say Hi";
    greeting = "say Hello instead";//error : Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

也不是这个

```
 const greeting = "say Hi";
    const greeting = "say Hello instead";//error : Identifier 'greeting' has already been declared 
```

Enter fullscreen mode Exit fullscreen mode

因此，每个 const 声明都必须在声明时初始化。
对于用`const`声明的对象，这种行为有些不同。虽然不能更新`const`对象，但是可以更新该对象的属性。因此，如果我们将一个`const`对象声明为这个

```
 const greeting = {
        message : "say Hi",
        times : 4
    } 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们不能这样做

```
 const greeting = {
        words : "Hello",
        number : "five"
    }//error :  Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样做

```
 greeting.message = "say Hello instead"; 
```

Enter fullscreen mode Exit fullscreen mode

这将更新`greeting.message`的值，而不会返回错误。

**`const`**吊装

就像`let`，`const`声明被提升到顶部但是没有初始化。

为了以防万一，你错过了不同之处，它们在这里:

1.  `var`声明是全局作用域或函数作用域，而`let`和`const`是块作用域。

2.  `var`变量可以在其作用域内更新和重新声明；`let`变量可以更新，但不能重新声明；`const`变量既不能更新也不能重新声明。

3.  它们都被提升到它们作用域的顶部，但是当`var`变量用`undefined`初始化时，`let`和`const`变量没有被初始化。

4.  `var`和`let`可以不初始化就声明，`const`在声明时必须初始化。

有什么问题或补充吗？请留言评论。

感谢您的阅读:)