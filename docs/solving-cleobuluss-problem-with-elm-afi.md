# 用 Elm 计分拼字游戏单词

> 原文：<https://dev.to/vincecampanale/solving-cleobuluss-problem-with-elm-afi>

[![Cleobulus Rhodes](img/020402d7ba611449cd342cc0131e81b6.png)T2】](https://commons.wikimedia.org/wiki/File%3ACleobulus_Rhodes.JPG "By Gortyna (Own work) [CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0)], via Wikimedia Commons")

### 问题

大约在公元前 6 世纪，希腊七贤之一的克莱俄布卢向世界展示了一种基于单词的智力游戏。他称之为“拼字游戏”。在概述他的游戏时，他省略了一个重要的部分，也许是故意的——如何计算一个单词的分数。一个普遍的解决方案尚未出现。

### 假设

为了提供一个合理的解决方案，我们必须先做一些假设。

```
1) The letters A, E, I, O, U, L, N, R, S, and T have the value 1.
2) The letters D and G have the value 2\.  
3) The letters B, C, M, and P have the value 3\.  
4) The letters F, H, V, W, and Y have the value 4\.  
5) The letter K has the value 5\.  
6) The letters J and X have the value 8\.  
7) The letters Q and Z have the value 10\. 
```

Enter fullscreen mode Exit fullscreen mode

### 一解

让我们开始吧。

对于像这样的简单脚本问题，分解问题并用伪代码概述解决方案是很好的，所以让我们从这里开始。

```
-- given a word
-- split the word up into letters
-- get the points value for each letter
-- store / record the point values for each letter
-- add up all the point values
-- return the sum 
```

Enter fullscreen mode Exit fullscreen mode

我发现在 Elm 中构造函数时从类型签名开始很有帮助。我们要构建的函数将接受一个单词并返回一个分数。用编译器可以理解的术语来说，它将接受一个`String`并返回一个`Int`。

```
scoreWord: String -> Int 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以开始构建我们的函数。

```
scoreWord: String -> Int
scoreWord word = 
   word 
```

Enter fullscreen mode Exit fullscreen mode

好的，所以把单词拆分成字母很容易:

```
scoreWord: String -> Int
scoreWord word = 
  String.split "" word 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要得到每个字母的分值。这给我们的问题带来了一点复杂性——点的值来自哪里？埃尔姆不知道一封信值多少钱。一种方法是做一个记录，一系列的键值对。

```
pointsCategories =
    { one = [ "A", "E", "I", "O", "U", "L", "N", "R", "S", "T" ]
    , two = [ "D", "G" ]
    , three = [ "B", "C", "M", "P" ]
    , four = [ "F", "H", "V", "W", "Y" ]
    , five = [ "K" ]
    , eight = [ "J", "X" ]
    , ten = [ "Q", "Z" ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们记录的每个键都是点值的 word 版本。每个值都是一个列表，其中包含了具有该点值的每个字母。Elm 中的记录不能有键的数字文字，所以这是我们能得到的最接近的。

现在，我们需要一个函数，通过在`pointsCategories`记录中查找一个字母的点值并将其转换为一个数字来对该字母进行评分。

```
getPointsForLetter : String -> Int
getPointsForLetter letter =
    if List.member letter pointsCategories.one then
        1
    else if List.member letter pointsCategories.two then
        2
    else if List.member letter pointsCategories.three then
        3
    else if List.member letter pointsCategories.four then
        4
    else if List.member letter pointsCategories.five then
        5
    else if List.member letter pointsCategories.eight then
        8
    else if List.member letter pointsCategories.ten then
        10
    else
        0 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这个功能有点(非常)多余。我开始觉得这个程序有点臭，需要整理一下，但是让我们在重构之前让这个解决方案工作起来。

这将是一个很好的时机来注意我们的`pointsCategories`记录中的所有字母都是大写的，但是我们不能保证给定的单词都是大写的，除非你在玩一个非常大声的克莱俄布卢游戏版本。

为了解决这个问题，我们最好把所有的字母都大写，因为这是它们在`pointsCategories`记录中出现的方式——充满活力！

```
scoreWord : String -> Int
scoreWord word =
  List.map String.toUpper (String.split "" word) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个大写字母列表，所以我们可以使用我们的`getPointsValue`函数将字母转换成点。

```
scoreWord : String -> Int
scoreWord word = 
  List.map getPointsForLetter (List.map String.toUpper (String.split "" word)) 
```

Enter fullscreen mode Exit fullscreen mode

为了利用 Elm 出色的管道操作符使我们的解决方案具有可读性，这些括号已经超出了我们的时间。

管道运营商很好，因为它把

```
f(g(h(i(x)))) 
```

Enter fullscreen mode Exit fullscreen mode

进入

```
x 
  |> i 
  |> h 
  |> g 
  |> f 
```

Enter fullscreen mode Exit fullscreen mode

其中`x`是某个值，`i`、`h`、`g`和`f`是函数。

将这个漂亮的工具应用到我们的解决方案中，我们得到:

```
scoreWord : String -> Int
scoreWord word =
  word 
    |> String.split ""
    |> List.map String.toUpper
    |> List.map getPointsForLetter 
```

Enter fullscreen mode Exit fullscreen mode

敏锐的读者会注意到我们还没有返回一个`Int`，而是返回一个`Int`的列表(或者使用 Elm 语法的`List Int`)。让我们解决这个问题。

```
scoreWord : String -> Int
scoreWord word = 
  word 
    |> String.split ""
    |> List.map String.toUpper
    |> List.map getPointsForLetter
    |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案是可行的，但是我讨厌`getPointsForLetter`函数中`if else if`的重复链。不幸的是，这是解决方案的症结所在，所以这次重构将是一次大的重构！

### 全部登上重构拖拉机

上述解决方案的主要问题是，我们不能在`pointsCategories`记录中使用数字文字作为键。这意味着我们将查找值和将它转换成一个`Int`分开。为了解决这个问题，我们需要打开工具箱，找到一个新的数据结构。我建议我们用一本字典(`Dict`)把一个数字和一个字符联系起来。我们需要导入`Dict`才能使用。

```
import Dict exposing (..)

pointValues : Dict Char Int
pointValues =
    Dict.fromList
        [ ( 'A', 1 )
        , ( 'E', 1 )
        , ( 'I', 1 )
        , ( 'O', 1 )
        , ( 'U', 1 )
        , ( 'L', 1 )
        , ( 'N', 1 )
        , ( 'R', 1 )
        , ( 'S', 1 )
        , ( 'T', 1 )
        , ( 'D', 2 )
        , ( 'G', 2 )
        , ( 'B', 3 )
        , ( 'C', 3 )
        , ( 'M', 3 )
        , ( 'P', 3 )
        , ( 'F', 4 )
        , ( 'H', 4 )
        , ( 'V', 4 )
        , ( 'M', 4 )
        , ( 'Y', 4 )
        , ( 'K', 5 )
        , ( 'J', 8 )
        , ( 'X', 8 )
        , ( 'Q', 10 )
        , ( 'Z', 10 )
        ] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`Dict.get`方法访问字母的点值。这种数据结构的改变是至关重要的，因为我们已经将冗余从程序中移除，并转移到数据本身中。数据中的冗余是肉汁，代码中的冗余是浆糊。

让我们写一个函数，它接受一个字符并返回它的点值。

```
getPointValue : Char -> Int
getPointValue letter = 
    let
        letterScore =
            Dict.get letter pointValues
    in
    case letterScore of
        Just score ->
            score

        Nothing ->
            0 
```

Enter fullscreen mode Exit fullscreen mode

`Dict.get`不能保证返回某个值，因为它可以用一个可能与字典中的键不对应的值来调用，就像我们试图获取一个汉字的点值一样。按照 Elm 的编译器能够理解的术语，`Dict.get`返回一个`Maybe`类型，所以我们需要告诉程序如何处理`Maybe`类型- `Just`(我们得到了一些东西)和`Nothing`(没有运气)。

如果我们得到一个分数，然后返回那个分数。如果没有，默认为 0。

是时候重写`scoreWord`了，它将我们的单词拆分成一个由`Char`(`String.toList`)组成的列表，并使用我们新的`getPointValue`函数来获取列表中每个字符的值。

```
scoreWord : String -> Int
scoreWord word =
    word
        |> String.toUpper
        |> String.toList
        |> List.map getPointValue
        |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但我仍然认为`getPointValue`有点冗长。让我们用几个小技巧让它更容易理解。

`flip`是一个简洁的工具，可以让你改变函数参数的顺序。`Maybe.withDefault`是我们在`case`语句中所做的事情的简写方式。利用这两个方便的机制和管道操作符，我们可以这样写`getPointValue`:

```
getPointValue : Char -> Int
getPointValue letter =
  letter 
    |> flip Dict.get pointValues
    |> Maybe.withDefault 0 
```

Enter fullscreen mode Exit fullscreen mode

啊，好多了。

为了结束这篇文章，下面是整个脚本:

```
module ScrabbleScore exposing (..)

import Dict exposing (..)

pointValues : Dict Char Int
pointValues =
    Dict.fromList
        [ ( 'A', 1 )
        , ( 'E', 1 )
        , ( 'I', 1 )
        , ( 'O', 1 )
        , ( 'U', 1 )
        , ( 'L', 1 )
        , ( 'N', 1 )
        , ( 'R', 1 )
        , ( 'S', 1 )
        , ( 'T', 1 )
        , ( 'D', 2 )
        , ( 'G', 2 )
        , ( 'B', 3 )
        , ( 'C', 3 )
        , ( 'M', 3 )
        , ( 'P', 3 )
        , ( 'F', 4 )
        , ( 'H', 4 )
        , ( 'V', 4 )
        , ( 'M', 4 )
        , ( 'Y', 4 )
        , ( 'K', 5 )
        , ( 'J', 8 )
        , ( 'X', 8 )
        , ( 'Q', 10 )
        , ( 'Z', 10 )
        ]

getPointValue : Char -> Int
getPointValue letter =
    letter
        |> flip Dict.get pointValues
        |> Maybe.withDefault 0

scoreWord : String -> Int
scoreWord word =
    word
        |> String.toUpper
        |> String.toList
        |> List.map getPointValue
        |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们永远无法确切知道克莱俄布卢打算如何为一个词打分，但我们可以给出我们最好的猜测，这是我的猜测。

* * *

*如果你喜欢我的风格，[看看我的博客](http://www.vincecampanale.com/blog/)或者在 twitter 上关注我，地址是[@ _ Vince campanele](https://twitter.com/_vincecampanale)，在迷因和转发之间偶尔会发现一些网络开发的好东西:*

这是我编的。