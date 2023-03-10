# Elm 形 3:使用 Elm 核心列表和字符串函数检测字谜

> 原文：<https://dev.to/vincecampanale/elm-kata-3-using-elm-core-list-and-string-functions-to-detect-anagrams-1ba>

欢迎来到**榆树形**的第三部分，在这个系列中，我们通过解决不同的练习来了解榆树的来龙去脉。

### 问题

另一天，另一个问题要解决。叫我夏洛克。以下是我们正在处理的问题陈述，由 exercism.io 提供:

> 给定一个单词和一个可能的变位词列表，选择正确的子列表。给定“listen”和类似“enlists”“Google”“inlets”“banana”的候选列表，程序应该返回一个包含“inlets”的列表。

### 回合 1

像往常一样，我喜欢从函数签名开始，所以让我们从这里开始:

```
detect : String -> List String -> List String 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个函数，它接受一个单词和一个单词列表，并返回一个新列表，其中包含给定列表中原始单词的所有变位词(如果有)。

我看到这个问题的第一个想法是“排列”如果我可以生成一个给定单词中所有字母组合的列表，并检查该列表中给定的可能候选单词列表中的每个单词，我最终会得到一个变位词列表。

我现在将展示这个解决方案，但是请记住，还有一个更好的方法。如果你已经看到了，在我看到容易的方法之前，我花了整个过程来解决这个困难的方法-我觉得孔子如果听到我这么说，他会故意点头。

这是第一个辉煌的关口:

```
module Anagram exposing (..)

import List.Extra exposing (permutations)

getPermutationsOf : String -> List String
getPermutationsOf word =
    word
        |> String.toList
        |> List.Extra.permutations
        |> List.map String.fromList

isPermutationOf : String -> String -> Bool
isPermutationOf word other =
    let
        uppercaseWord =
            String.toUpper word

        uppercaseOther =
            String.toUpper other
    in
        not (uppercaseWord == uppercaseOther)
            && List.member uppercaseOther (getPermutationsOf uppercaseWord)

detect : String -> List String -> List String
detect word possibleMatches =
    possibleMatches
        |> List.filter (isPermutationOf word) 
```

Enter fullscreen mode Exit fullscreen mode

只是提醒一下，如果你想自己运行这个，你需要运行`elm-package install elm-community/list-extra`来访问这个奇特的排列方法。

因此，请允许我向您介绍一下这个解决方案。让我们从最后一个函数开始，一步一步地解开程序。

在`detect`中，我过滤`possibleMatches`列表中的单词，这些单词是我正在寻找的变位词的排列。为了做到这一点，我需要一个助手函数来告诉我一个单词是否是另一个单词的排列，这就是程序中的第二个函数。

`isPermutationOf`取两个单词并返回第二个单词是否出现在第一个单词的排列列表中。首先，我将这两个单词转换成大写(以处理边缘情况和奇怪的字符)，然后我确保这两个单词不完全相同(另一个边缘情况)，然后我检查第二个单词的排列列表。

不过，获取置换列表也需要一些工作。因此程序中的第一个函数。`getPermutationsOf`是我实际使用`List.extra.permutations`实用程序的地方。我取这个单词，把它转换成一个字符列表，再把它转换成一个列表列表(每个列表包含一个可能的字符顺序列表)，然后映射这个列表列表，把它转换回一个字符串列表。

结果是一个非常慢，非常暴力，但非常正确的解决方案。

### 第二回合

我当初怎么没发现呢？我-哦-我的，这可以做得更干净。

与其改变原来的单词来方便列表的检查，为什么我们不改变列表中的所有单词来方便单词的检查呢？！

我们如何检查两个单词是相同的，只是字母都混在一起了？通过排序和比较它们的排序形式！

首先，我们需要一个适用于字符串的排序函数:

```
sortStr = String.toList >> List.sort >> String.fromList 
```

Enter fullscreen mode Exit fullscreen mode

对我来说,`>>`是新的——它可以让你将函数组合在一起，得到更大、更糟糕的函数。它类似于管道(`|>`)操作符，除了您没有传入任何参数，您只是将函数混合在一起以达到更高的目的。

现在我们需要把我们的`sortStr`函数放在一个接受两个单词的函数中，看看它们是否是字谜！

```
isAnagram word possibleMatch =
  not (String.toUpper word == String.toUpper possibleMatch)
    && sortStr (String.toUpper word) 
    == sortStr (String.toUpper possibleMatch) 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用我们的助手来制作一个聪明的`detect`函数，使我们的旧`detect`函数看起来像婴儿食品。

```
detect : String -> List String -> List String
detect word possibleMatches =
    let
        sortStr =
            String.toList >> List.sort >> String.fromList

        isAnagram possibleMatch =
            not (String.toUpper word == String.toUpper possibleMatch)
                && sortStr (String.toUpper word)
                == sortStr (String.toUpper possibleMatch)
    in
        List.filter isAnagram possibleMatches 
```

Enter fullscreen mode Exit fullscreen mode

嘣，你有它。

关注[@ _ Vince campanele](https://twitter.com/_vincecampanale)以获取**榆树形**系列的未来更新。还有，这会让我自我感觉良好。

感谢您的时间< 3，希望这是值得的。