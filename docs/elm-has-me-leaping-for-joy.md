# “榆树让我高兴得跳起来了”

> 原文：<https://dev.to/vincecampanale/elm-has-me-leaping-for-joy>

[![](img/66a02542d3b99a0c68d1123b42dcccbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IQqqh9Oq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://frontendmasters.com/assets/Elm.png)

我是 Elm 的新手(几周前)，正在做关于 exercism.io 的练习。我发现这种语言有很大的潜力，我很享受这个学习过程。写下我学到的东西通常有助于我更好地理解它。于是，本文诞生了。

练习很简单:用榆树识别闰年。因此，给定一年，确定这一年是否是闰年。规则如下:

```
leap years are on: 
- every year that is evenly divisible by 4
- except every year that is evenly divisible by 100
- unless the year is also evenly divisible by 400 
```

让我们从编写一些函数来检查这些整除规则开始。

```
divisibleBy4 year = year % 4 == 0
divisibleBy100 year = year % 100 == 0
divisibleBy400 year = year % 400 == 0 
```

现在对于布尔表达式...

```
(divisibleBy4 year) && (not (divisibleBy100 year) || (divisibleBy400 year)) 
```

酷毙了。所以现在我们可以把这些碎片放在一起，构建我们的函数。但是首先，一个函数签名！

我们需要一个接受整数(`Int`)并返回布尔值(`Bool`)的函数:

```
isLeapYear : Int -> Bool 
```

砰。

现在，我们可以添加一个`let-in`表达式来为我们的`divisibleByX`助手创建一个局部函数范围。

```
isLeapYear year =
  let
    divisibleBy4 y = 
        y % 4 == 0

    divisibleBy100 y =
      y % 100 == 0

    divisibleBy400 y =
      y % 400 == 0
  in 
    (divisibleBy4 year) && (not (divisibleBy100 year) || (divisibleBy400 year)) 
```

这实际上可以好得多。

首先，我们可以让`divisibleByX`使用两个参数，而不是一个:要除的数和被除的数，这样会更有用。

```
divisibleBy n y =
 y % n == 0 
```

很好，但还有更多。Elm 的核心库之一“Basics”有一个名为`rem`的函数，它具有以下类型签名:

```
rem : Int -> Int -> Int 
```

根据文献记载，它是这样做的:

```
Find the remainder after dividing one number by another.

e.g. rem 11 4 == 3 
```

这正是我们所需要的吗？是的，这正是我们所需要的。

```
divisibleBy n y = rem y n == 0 
```

噪音。是时候重构了。

```
isLeapYear : Int -> Bool
isLeapYear y =
    let
        divisibleBy n y =
            rem y n == 0
    in
        (divisibleBy 4 y) && (not (divisibleBy 100 y) || (divisibleBy 400 y)) 
```

布尔表达式看起来可以简化。基础包中还有另一个有趣的布尔运算符...`xor`。

```
xor : Bool -> Bool -> Bool

The exclusive-or operator. True if exactly one input is True. 
```

酷豆。让我们重构我们的解决方案。

如果我们再次阅读我们的需求，这个问题实际上非常适合于`xor`。除了能被 100 整除的年份，我们希望所有的年份都能被 4 整除。

```
xor (divisibleBy 4 year) (divisibleBy 100 year) || (divisibleBy 400 year) 
```

这留给我们第三次迭代:

```
isLeapYear : Int -> Bool
isLeapYear year =
    let
        divisibleBy n y =
            rem y n == 0
    in
        xor (divisibleBy 4 year) (divisibleBy 100 year) || (divisibleBy 400 year) 
```

我甚至还没有触及这种强大语言的皮毛。进入 FRP 思维模式是一个挑战，但是我已经看到了用这种模式思考的好处。虽然我在最后几段中总结的解决方案已经完成了工作，但肯定还有更好的方法...

请在 Twitter 上关注我，地址是[@ _ Vince campanele](https://twitter.com/_vincecampanale)和/或 [Github](https://github.com/vincecampanale) 以获取更多关于榆树的信息...