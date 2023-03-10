# 向自己解释携带

> 原文：<https://dev.to/damcosset/explaining-currying-to-myself>

## 简介

好吧，又到了自学我不懂的东西的时候了...在这篇文章中，我们将探索一种叫做谄媚的技巧。Currying 是函数式编程中的另一个概念。它是这样的:当一个函数需要几个参数时，我们把它分解成连续的链式函数，每个函数只需要一个参数。

我们将每个函数的 **arity** 减少为 1(arity 是函数的参数长度)。让我们看一些例子。

注意:在进一步学习之前，您应该理解闭包。难道说有了[这个](https://dev.to/damcosset/explaining-closures-to-myself)？

## 简单的奉承

先说一个非咖喱的例子。我们想计算一个函数的参数之和:

```
const sum = (...args) => {
  let sum = 0
  for( let i = 0; i < args.length; i++ ) {
    sum += args[i]
  } 
  return sum
}

sum( 1, 2, 3, 4, 5, 6, 8) //29 
```

sum 函数的简化版本如下:

```
sumCurry(1)(2)(3)(4)(5)(6)(8) //29 
```

那么我们如何实现 currying 呢？好吧，让我们试试这样的东西:

```
const curried = 
  ( func, arity = func.length, nextFunc ) =>
    (nextFunc = prevArgs => 
      nextArg => {
        let args = prevArgs.concat( [nextArg] )

        if( args.length >= arity ){
          return func( ...args )
        } 
        else {
          return nextFunc( args )
        }
      })( [] )

const sumCurry7 = curried( sum, 7)
sumCurry7(1)(2)(3)(4)(5)(6)(8) // 29 
```

好了，让我们把*的 curried* 函数分解一下。

它需要 2 个参数，第一个是最后要调用的函数(在我们的例子中是 sum)，第二个是我们期望的 arity(在调用 sum 之前我们想要的参数个数)。

此函数返回一个 IIFE(立即调用的函数表达式)。这个函数在第一次调用时接受一个空数组作为参数。它返回另一个函数，将 curry 序列中的下一个参数作为一个参数，该函数将它添加到我们的参数集合中。在第一次调用时，我们的数组是空的，所以变量 args 等于[ 1 ]。

我们检查是否有足够的参数来调用我们的原始函数。如果没有，我们创建另一个 curried 函数并继续收集参数。如果我们这样做了，我们就用所有的参数调用 *sum* 。

## 再比如

让我们想象我们在一家餐馆里。我们可以点一道菜和一份甜点。我们的订单有四个不同的组成部分，顾客 id、桌号、菜肴和甜点。

我们的 curried 函数看起来像这样:

`curriedOrder(customerId)(tableNumber)(dish)(dessert)`

让我们创建一个只返回订单的函数:

```
const displayOrder = (...infos) => {
  let order = `Customer ${infos[0].toUpperCase()} at table ${infos[1]} wants ${infos[2]}  ${infos[3] ? `and ${infos[3]} for dessert.` : '.'}`
  return order
} 
```

这里没什么特别的。让我们用之前创建的函数来处理这个问题:

```
const mealOneDish = curried( displayOrder, 3 )
const mealWithDessert = curried( displayOrder, 4)

const orderOne = mealOneDish('Joe')(3)('cheeseburger')
// Customer JOE at table 3 wants cheeseburger .

const orderTwo = mealWithDessert('Sarah')(6)('fries')('waffles')
// Customer SARAH at table 6 wants fries and waffles for dessert. 
```

## 为什么要用阿谀奉承？

使用像 currying 这样的技术的第一个原因是，您可以在代码库中分隔指定参数的时间和位置。您可能没有立即调用 *sum* 函数所需的所有参数，或者打印出所有订单信息。

客户进了餐厅，你还不知道她会坐在哪里。她的名字叫乔安娜。

`const johanna = mealWithDessert('Johanna')`

她走来走去，最后决定坐在 7 号桌。

`const johannaTable7 = johanna(7)`

服务员请她点菜。

`const johannaT7Order = johannaTable7('nuggets')`

*   你想要甜食吗？
*   不，谢谢你

```
johannaT7Order() // Customer JOHANNA at table 7 wants Nuggets . 
```

*   等等，我改变主意了。我要一份冰淇淋作为甜点。

```
johannaT7Order('ice cream') //Customer JOHANNA at table 7 wants Nuggets and ice cream for dessert. 
```

如果我们想用前面的函数计算一个和，也可以应用同样的问题。

```
const sumCurry4 = curried( sum, 4) 
// I want to calculate the sum of four numbers, I don't know them yet.

const firstNumber = sumCurry4(3) // Ok first one

//...Doing other important stuff ...

const secondAndThird = firstNumber(5)(20) // I need one more

//...Feeding the cat...

const final = secondAndThird(2) // 3 + 5 + 20 + 2 = 30 
```

如果我们还没有所需的参数数量，我们的 curry 将返回一个函数。只有当所有参数都存在时，我们才调用原始函数。

使用 curried 函数的第二个原因是，使用一元(单参数)函数要容易得多。组合对于单参数函数更有效，并且是函数式编程的一个关键组件。

我也喜欢它使代码更具可读性，但这可能是个人偏好。

嗯，这是关于奉承的介绍。我希望我已经足够清楚这个我自己仍在探索的主题。我想我本可以用我的*curred*实现走得更远一点，并且也 curry 那个...欢迎任何反馈/整改！