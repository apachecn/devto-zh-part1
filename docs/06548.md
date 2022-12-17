# 平面图解释

> 原文：<https://dev.to/jonstodle/flatmap-explained-3oo>

我最近在帮助一个熟人理解`flatMap`(或者在 Rx 中被称为`SelectMany`)。网)。在帮他设置好观察台后，他注意到`flatMap`几乎就像魔术一样。

我记得当我第一次学习反应式扩展时，`flatMap`是我并不真正理解的操作符之一。当我使用它时，主要是因为我被告知(或在某处读到)这是我必须使用的。

在这篇文章中，我将尝试解释一下`flatMap`是如何工作的(从用户的角度)以及如何在你的 Rx 代码中使用它。

# 地图

首先，我们必须谈谈`map`操作符:`map`返回值。这就是它的全部工作。

当前值作为参数传入。这样，当前值可以用来以某种方式影响返回值，但这完全取决于程序员。

这里有几个如何使用`map`的例子:

### 乘以一个当前值

```
myObservable // This outputs 1--2--3--4--5--6--  
  .map(currentValue => currentValue * 2) // Multiply by 2
  .subscribe(console.log);

OUTPUT:  
2  
4  
6  
8  
10  
12 
```

[在 JS 箱上](http://jsbin.com/buqemim/2/edit?js,console)

### 转换当前值

```
myObservable // This outputs 1--2--3--4--5--6--  
  .map(currentValue => currentValue + "") // Convert to string
  .subscribe(console.log);

OUTPUT:  
"1"
"2"
"3"
"4"
"5"
"6" 
```

[在 JS 箱上](http://jsbin.com/kezamo/3/edit?js,console)

### 转换为常数值

```
myObservable // This outputs 1--2--3--4--5--6--  
  .map(currentValue => "It has happened") // Always return the same value
  .subscribe(console.log);

OUPUT:  
"It has happened"
"It has happened"
"It has happened"
"It has happened"
"It has happened"
"It has happened" 
```

[在 JS 箱上](http://jsbin.com/diwayeg/2/edit?js,console)

# 合并

让我们把多个观察值合并成一个观察值。为了合并可观测量，它们必须输出相同类型的值。

如果你有三个按钮在三个不同的观察值中产生值 1、2 和 3，你可以使用 merge 把所有的值放到一个单独的观察值中:

```
Rx.Observable.merge(  
    button1Click$.map(_ => 1),
    button2Click$.map(_ => 2),
    button3Click$.map(_ => 3))
  .subscribe(console.log);

OUTPUT:  
1  
2  
3  
3  
2  
1 
```

[在 JS 箱上](http://jsbin.com/wafeguy/2/edit?html,js,console,output)

你可以合并两个按钮的可观察值，并将点击映射到`+1`和`-1`，制作一个简单的计数器:

```
Rx.Observable.merge(  
    minusButtonClick$.map(_ => -1),
    plusButtonClick$.map(_ => +1))
  .startWith(0)
  .scan((aggr, curr) => aggr + curr, 0)
  .subscribe(console.log);

OUTPUT:  
0  
1  
2  
3  
2  
1 
```

[在 JS 箱上](http://jsbin.com/vomijej/edit?html,js,console,output)

# 平面图

我们终于开始了解`flatMap`本身了。我们绕过`map`和`merge`的原因是`flatMap`主要是两者的结合。

当您使用`flatMap`时，会发生两件事:您提供一个返回可观察对象的函数，RxJS 将该可观察对象与`flatMap`所属的可观察对象合并。

```
buttonClick$  
  .flatMap(_ => Rx.Observable.timer(1000).map(_ => "Delayed click"))
  .subscribe(console.log);

OUTPUT:  
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click" 
```

[在 JS 箱上](http://jsbin.com/huxunel/2/edit?html,js,console,output)

`flatMap`基本上只是一个常规的`map`，但是它返回一个可观察的值，而不是直接返回一个值。

**每当你觉得有必要订阅一个可观察对象中的可观察对象时，你就会想要`flatMap`。**

# 结论

我希望我已经设法澄清了一点(而不是让你更困惑)。`flatMap`是一个强大的工具，理解它会让你更精通 Rx。

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/flatmap-selectmany-explained/)T3】*