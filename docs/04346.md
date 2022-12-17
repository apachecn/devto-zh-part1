# JavaScript 中的默认参数

> 原文：<https://dev.to/tylermcginnis/default-parameters-in-javascript-5ia>

在这个视频/帖子中，您将了解 ES6(es 2015)的默认参数如何允许您在调用函数时为任何未定义的参数设置默认值。您还将学习如何使用默认参数来使某些参数成为必需的。

### 视频

[https://www.youtube.com/embed/aF3-ub5bkXQ](https://www.youtube.com/embed/aF3-ub5bkXQ)

### 岗位

下面我们有一个函数叫做`calculatePayment`。这个函数将接受三个参数:`price`、`salesTax`和一个`discount`。该函数的目标是在考虑销售税和任何折扣后，获取这三个商品并返回最终价格。关于这个函数，有趣的是唯一真正需要的参数是`price`。当我们定义`calculatePayment`时，我们应该为`salesTax`和`discount`设置默认值，这样如果在调用函数时没有指定它们，它们仍然会用这些值初始化。

对于 ES5，我们通常会这样做。

```
function calculatePayment (price, salesTax, discount) {
  salesTax = salesTax || 0.047
  discount = discount || 0

  // math
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未见过这样使用的`||`操作符，下面是怎么回事。当`calculatePayment`被调用时，`salesTax`将被设置为`salesTax`是，除非它是 falsy，那么它将被设置为`0.047`。同样的事情也发生在`discount`身上。

如果您是 obeservant，您可能已经注意到当前实现的一些问题。如果我们调用 calculatePayment 时传入`100`、`0`和`0`，会发生什么？

```
calculatePayment(100,0,0) 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望`salesTax`和`discount`都被设置为`0`，因为这是我们在调用函数时指定的。但是，在 JavaScript 中，`0`是 falsy。因此，`salesTax`不是我们指定的`0`，而是被设置为我们的默认值`0.047`。为了解决这个问题，我们可以使用`typeof`操作符，而不是依赖`||`操作符。

```
function calculatePayment (price, salesTax, discount) {
  salesTax = typeof salesTax === 'undefined' ? 0.047 : salesTax
  discount = typeof discount === 'undefined' ? 0 : discount

  // math
} 
```

Enter fullscreen mode Exit fullscreen mode

好多了。现在，`salesTax`将会是`0`，正如我们所预期的那样。如果你还和我在一起，你现在很容易理解 ES6 的默认参数的附加值，因为它们解决了同样的问题。然而，不使用`typeof`操作符来检查值是否未定义，我们可以这样做，

```
function calculatePayment(price, salesTax = 0.047, discount = 0) {
  console.log('tax', salesTax)
  console.log('discount', discount)

  // math
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们所做的只是将逻辑上移至定义函数参数的位置。干净多了。

这通常是关于默认参数的帖子的结尾。然而，我认为默认参数还有一个更酷|更怪异|更聪明的方面值得一提。

回头看看`calculatePayment`函数，我提到该函数唯一真正需要的参数是`price`。我们可以为其他所有东西设置一个默认值，但是如果没有传入`price`,函数就会中断。如果有一种方法，使用默认参数，让我们的函数抛出一个错误，如果调用函数时`price`是`undefined`，那会怎么样？你大概能猜到，是有的。

首先，让我们创建一个名为`isRequired`的函数，它的全部目的就是抛出一个错误。

```
function isRequired (name) {
  throw new Error(name + 'is required')
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，类似于我们之前对`salesTax`和`discount`所做的，让我们在`calculatePayment`的参数中设置`price`等于我们的`isRequired`函数的函数调用。

```
function isRequired (name) {
  throw new Error(name + 'is required')
}

function calculatePayment(
  price = isRequired('price'),
  salesTax = 0.047,
  discount = 0
) {

    //math
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们调用`calculatePayment`而没有传入一个`price`，我们将得到一个错误。

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，作为他们在 T2 现代 JavaScript 课程的一部分