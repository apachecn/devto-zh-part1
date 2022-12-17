# 什么是函数式编程？

> 原文：<https://dev.to/mortoray/what-is-functional-programming>

三个关键概念构成了函数式编程的本质:一阶函数、纯函数和不可变数据。它们一起表达了关于代码单元如何相互作用的基本架构范例。虽然有许多语言专门研究这种范式，但它已经被广泛接受，可以用于任何通用语言。

## 为什么？

函数式编程的价值在于它能够在高层次上表达数据的转换。我们不需要深入了解计算机如何工作的细节。程序员开始主要考虑数据和计算领域。

函数式编程创建了一个稳定的系统。代码不能破坏它运行的环境。它是可预测的和确定的。给定范例上的约束，编译器有很大的余地来优化和创建高效的可执行文件。

纯函数本质上是计算的黄金标准。它们易于理解，具有单一的目的和清晰的输入输出映射。这使得它们易于测试、使用和维护。

> 本文主要关注函数式编程的好处和核心品质。和所有范例一样，都有局限性。我将在以后的文章中讨论这些内容。

## 纯函数

如果一个函数的结果仅仅基于它的输入参数，并且不会产生任何副作用，那么这个函数就是纯函数。我们来看这两点。

### 从参数中评估

当给定相同的输入参数时，纯函数的结果总是相同的。没有隐藏状态可以修改值的结果。

```
var total = sum( collection )
var C = matrix_mult( A, B )
var safe_name = encode_uri_comp( user_name ) 
```

Enter fullscreen mode Exit fullscreen mode

给定一个特定的`collection`，`total`将总是具有相同的值。`matrix_mult`将表现得像数学定义一样，给出乘法的单个有效结果。一个标准规定了由`encode_uri_comp`完成的转换，并且它总是相同的。

这与包含隐式或隐藏状态的函数形成了强烈的对比。

```
var value = random( 100 )
var time = get_current_time()
var next = pop( stack ) 
```

Enter fullscreen mode Exit fullscreen mode

当再次调用时，这些函数的结果将会不同。`random`的结果基于它的参数，它将返回一个从 0 到 100 的值。然而，它不会每次都返回相同的值，而是依靠隐藏状态来返回不可预测的值。`get_current_time`甚至不接受参数，但每次都会产生一个新值。`pop`只依赖于它的参数，但是因为它修改了`stack`，所以下次它不会返回相同的值。

### 无副作用

一个纯粹的功能不会产生副作用。这意味着系统的状态在调用前后是相同的。看看一些反例，这些函数会产生副作用:

```
print( "Hello" )
stack.push( value )
var handle = open( "file.txt", write ) 
```

Enter fullscreen mode Exit fullscreen mode

这些会改变用户的控制台、内存中的数据结构或文件系统。它们导致系统状态的改变，因此不是纯粹的函数。状态的改变会改变不纯函数的结果。

我们只关心可观察到的副作用。出于实际原因，我们忽略了几个状态变化。为了提高效率，函数可以有一个内部缓存。缓存不影响结果，因此它不是一个可观察到的副作用。计算需要时间，但是我们不认为系统时间的变化会产生显著的影响。我们只是关注功能的高级输入/输出，以及用户如何感知系统。

> 出于定义和编译器实现的目的，我们忽略这些副作用。当然，在某些领域，内存分配和时间消耗的影响不容忽视。

## 不可变的数据

与 pure 方面相关的是不可变数据。这指的是在创建时获得一个值并且永远不能更改的数据结构。

```
var a = point(4,5)
var b = a.normal() 
```

Enter fullscreen mode Exit fullscreen mode

`a`的值将始终是`4,5`，它不能改变。`normal`创建一个新的`point`作为它的返回值。函数范式关注的是在不修改输入数据的情况下将数据转换成新的形式。

对于不可变数据，按副本赋值和按值赋值没有区别。因为源值永远不能改变，所以赋值是如何完成的并不重要。这给了编译器优化的空间，但也最小化了读取代码时的思想开销。

不变量也可以提高线程安全性。一旦一个线程引用了一个对象，它就不再需要担心同步问题，因为那个对象不会改变。这也最小化了单线程代码中的排序问题。我们不必担心对象的当前状态，因为它只有一种状态。

> 不可变数据和无副作用函数几乎可以被认为是相同的特性。你不能只有效地使用其中的一个。他们合作得最好。

## 一阶函数

函数式编程将函数视为变量，就像数据一样。特别是，一个函数可以作为参数传递给另一个函数。这是一种有助于减少代码冗余的泛型编程形式。

```
var sum = fold( numbers, add )
var product = fold( numbers, mul ) 
```

Enter fullscreen mode Exit fullscreen mode

`fold`是一个常见的函数概念，它在列表中的所有项目之间插入一个运算符，并计算结果。这里我们传递它`add`来执行求和，传递`mul`来获得乘积。

```
var compare = is_reverse ? greater_than | less_than
var abc = sort( names, compare ) 
```

Enter fullscreen mode Exit fullscreen mode

这种方法将函数绑定到一个命名变量，以便稍后在`compare`函数中使用。

### 兰姆达斯和闭包

具有函数特性的语言也提供了 lambda 函数和闭包。从技术上来说，这是两个不同的概念，但是倾向于在同一个特性中公开。

lambda 函数是一个匿名函数。这些经常被用作论据。

```
var ordered = sort( products, (a,b) -> { 
    return a.SKU < b.SKU
}) 
```

Enter fullscreen mode Exit fullscreen mode

闭包允许在函数范围之外使用变量，这就更进一步了。虽然经常与兰姆达斯，他们不需要。

```
var is_reverse = get_config( "product_reverse" )

var compare = (x,y)-> {
    return is_ordered ? x.SKU < b.SKU | b.SKU < a.SKU
}

var sorted = sort( products, compare ) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`compare`函数绑定到父作用域中定义的`is_ordered`变量。

## 表现力

在实现算法时，函数式编程是一个很有价值的工具。纯函数和不可变数据使得它在安全性和编译器优化方面非常有吸引力。一阶函数引入了一种表达方式来重用概念和限制代码冗余。

它通常被认为是一种纯粹的计算范式。它让我们在算法领域工作，而不受机器细节的束缚。这种单一的焦点可以提高生产率和清晰度。

只要有可能使用函数范式，它就会成为首选的解决方案。对于一个完整的程序来说，它自然地融合了[命令式](https://mortoray.com/2017/05/10/what-is-imperative-programming/)和反应式编程。