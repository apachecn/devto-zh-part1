# 理解 JavaScript 中的闭包(有美味的墨西哥食物)

> 原文：<https://dev.to/swergi0/understanding-closures-in-javascript-with-delicious-mexican-food>

闭包是我们在 JavaScript 程序中每天都会遇到的东西。所以我想用一个简单实用的例子来了解闭包是什么，以及如何使用它。

# 什么是闭包？

这里是我遇到的一些不同的闭包定义，为了让你更好地理解闭包的定义:

*   一个闭包就是当一个函数能够记住并访问它的词法范围时，即使这个函数是在它的词法范围之外执行的。

*   闭包是函数和编写该函数的词法环境的组合。

*   闭包是可以访问外部函数变量的内部函数。

一个闭包可以访问 3 个不同的作用域；它自己的范围和变量，它的封闭函数的范围和变量，以及全局范围和它的变量。闭包也可以访问外部函数的参数。

# 如何创建闭包

让我们创建一个简单的闭包例子。假设你在一家美味的墨西哥餐馆，我们需要一个函数返回另一个可以访问外部函数作用域的函数

```
function takeOrder(mexicanDish) {
  var orderCall = "Here is your " + mexicanDish + " with ";

  function insertIngredient(mainIngredient) {
    console.log(orderCall + mainIngredient);
  }

  return insertIngredient;
}

var steakBurrito = takeOrder('burrito');

steakBurrito('steak'); // "Here is your burrito with steak" 
```

Enter fullscreen mode Exit fullscreen mode

当我们第一次调用我们的`takeOrder()`函数时，我们传递给它一个我们想要的墨西哥食物类型的参数。这可以是他们提供的任何东西；墨西哥玉米卷、墨西哥卷饼、墨西哥玉米煎饼....你明白了。

重点是，当我们调用`takeOrder('burrito')`时，它返回给我们一个函数`insertIngredient()`。我们将它设置为一个变量，我们稍后会调用它，名为`steakBurrito`，因为这就是我们最终订单的样子。所以你可以想象，我们的变量，`steakBurrito`，实际上只是伪装的`insertIngredient()`函数，等待被调用。

在我们调用这个函数之前，我们可以看到内部函数`insertIngredient()`持有一个写在外部函数`takeOrder()`中的变量`orderCall`。

我们最后调用`steakBurrito()`并传入`'steak'`的主要成分，这导致我们的函数注销我们的最终`orderCall`。

在某些语言中，这可能会返回一个错误，因为当我们将它赋给变量时，技术上我们已经调用并运行了`takeOrder()`,它不应该再可用，因为函数已经完成了它的执行。但是感谢 JavaScript 中闭包的魔力，我们小小的内部函数仍然紧紧抓住外部函数中的变量。这本质上就是一个结束！

为了清楚起见，而且因为我还是很饿，我们也可以点一份鸡肉玉米卷！

```
var chickenTaco = takeOrder('taco');
chickenTaco('chicken'); // "Here is your taco with chicken" 
```

Enter fullscreen mode Exit fullscreen mode

闭包有大量的实际用途，并且有更复杂的方法来实现它们。这只是一个简单的例子，它帮助我在基本层面上确定了什么是闭包。希望这能帮助人们克服困难，进入理解 JavaScript 闭包的美妙时刻！