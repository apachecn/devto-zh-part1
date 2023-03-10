# 延续传递风格的背后。阿戈的实际例子

> 原文：<https://dev.to/theodesp/behind-continuations-passing-style-practical-examples-ingo>

[![lines](img/7254e99c3cfbcd0290df93d117e81e6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wszwuCPS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45yn6qhbqhrm3cby2zvs.jpg)

*在计算机科学和计算机编程中，* *延续* *是计算机程序控制状态的抽象表示。延续使程序控制状态具体化。*

延续传递风格(Continuation-passing style)是一种有时在[函数式编程](https://medium.com/r/?url=http%3A%2F%2Fwiki.c2.com%2F%3FFunctionalProgramming)语言中使用的编程风格。主要思想是，让一个函数将另一个函数作为参数，而不是让一个函数返回值，该参数将被用作结果操作的**通道**或**延续**。

> 您可以把这个函数想象成一个回调函数，它在任何时候都保持闭包的状态。

在延续传递式编程中，这是一种构造函数的方式，因此它们不允许返回。函数必须通过将其将要返回的值传递给作为显式参数传递给它的延续函数来模拟返回。因此，你可以说所有的延续传递式函数都是带参数的 gotos。

让我们看一个简单的 Go 例子，因为该语言支持闭包。

```
// Identity just maps a value passed to the same value
func identity(v int, next func(int)) {
   next(v)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我在这里使用了一个 **int** 。正如你所看到的，这个函数只接受一个参数 **v** 和一个函数 **next** ，后者接受一个整型值，并把参数传递给 next。

为了使用这个函数，你只需要给 next:
提供一个值来调用它

```
// main.go
identity(5, func(result int) {
   fmt.Println(result) // prints 5
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是:

没有返回值。只是一个接受另一个传递当前状态的函数的函数。

好，让我们看另一个例子，这次是阶乘。

```
// Recursive Example
func factorial(n int, next func(int)) {
   if n == 0 {
      next(1)
   } else {
      factorial(n-1, func(k int) {
         next(n * k)
      })
   }
}

factorial(5, func(result int) {
   fmt.Println(result) // prints 120
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们这次是递归的。只要 n 没有达到 0，我们就再次调用阶乘，这一次传递一个不同的 next 函数，该函数将计算下一个阶乘数并将 n 减 1。

让我们来看看 n=2 时这是如何展开的:

```
factorial(2, next) (1)
factorial(1, func(k int) { // n !== 0
         next(2 * k) // (2) 
      })
factorial(0, func(k int) { // n !== 0
         next(1 * k) // (3)
      })

next(1) // n == 0 on the 3rd call. This will feed a value to k. ï»¿So go backwards at the stack of calls.
next(1 * 1) // (3)
next(2 * 1) // (2)
fmt.Println(2) // (1) 
```

Enter fullscreen mode Exit fullscreen mode

还有一种方法是将结果传递给函数，这种方法对尾调用更友好。

```
// Tail recursive way
func factorial(n int, next func(int)) {
   tailFactorial(n, 1, next)
}
func tailFactorial(n int, a int, next func(int)) {
   if n == 0 {
      next(a)
   } else {
      tailFactorial(n-1, n*a, next)
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是如何处理函数内部的总计算结果以及创建的函数数量。

## 好吧，那么错误呢？

一旦一个程序在 CPS 中，它就打破了语言中的标准异常机制。幸运的是，在 CPS 中很容易实现异常。这导致了一种更标准的处理错误的方式。

比方说，你有一个函数，它执行一些可能抛出或返回错误的调用。在这种情况下，您希望以一种相关的延续函数的方式来捕捉它，以便您可以在那里传递错误值并适当地处理它。这样就保持了连续不变量。

为了做到这一点，让我们添加另一个函数参数调用它失败，这一次接受错误:

```
func factorial(n int, next func(int), fail func(e error)) {
   if n < 0 {
      fail(errors.New("Number is negative"))
   } else if n == 0 {
      next(1)
   } else {
      factorial(n-1, func(k int) {
         next(n * k)
      }, fail)
   }
}

func getFactorial(n int, next func(int)) {
   factorial(n, next, func(e error) {
      fmt.Println(e) // handle error case here and pass something ï»¿downwards
      next(n)
   })
}

getFactorial(-1, func(result int) {
   fmt.Println(result) // this will print the error case first and then print -1
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如您现在看到的，您可以利用闭包将应用程序的所有错误传递给 **fail** 函数，并在以后捕获它们。这将使您有机会在失败后执行恢复操作。然后，您可以使用 **next** 函数向下传递一个有效值。这类似于在某些语言中做一个 **try/catch** ，但是没有异常处理的负担，或者缺少一个语言特性。这些只是相互调用的函数。

请注意，在这种情况下，使用 Go，您可以在任何情况下传递 **fail** 延续中的任何错误处理程序，并在同一个函数中处理它们。

你在这里看到的优势是什么？它清楚地将您的错误处理程序分组到您可以重用的函数中。这也使你的代码更具可读性和一致性。

但是有时你可能会发现，你不能跟踪什么是应该被调用的，并且很难跟踪延续，特别是如果它们是多个尾部调用的话。一般来说，如果你保持简单，就不会有歧义。

## 裁决

就是这样。我希望您已经了解了什么是延续传递式编程，以及它们如何仅使用函数和闭包来帮助模拟某些语言特性。

## 习题

1.  实现一个函数来计算斐波那契数列直到 n。使用连续传递的风格。

2.  如果您使用递归实现了前面的函数，现在使用尾递归实现它。

3.  选择一种不同于 Go 的编程语言(例如 Javascript)。有哪些结构和语言特性可以帮助你使用延续传递方式开发函数？比较并评论你的想法。

4.  延续和承诺的区别是什么？

## 参考文献

[维基文章](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FContinuation-passing_style)

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。