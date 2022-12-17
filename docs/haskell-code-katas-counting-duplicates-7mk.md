# Haskell 代码卡塔斯:计数重复

> 原文：<https://dev.to/hector/haskell-code-katas-counting-duplicates-7mk>

在过去的几个星期里，我一直在用来自 [Codewars](https://www.codewars.com/) 的 Haskell 风味的代码形开始我的一天。今天我从下面的形开始，并认为这将是一个很好的练习来完成我的解决方案。

> 编写一个函数，该函数将返回在输入字符串中出现多次的不区分大小写的字母字符和数字的计数。可以假设输入字符串只包含字母(大写和小写)和数字。

为了帮助澄清这个形的规格， [Hspec](https://hspec.github.io/) 测试套件如下:

```
module Codwars.Kata.Duplicates.Test where

import Codwars.Kata.Duplicates (duplicateCount)
import Data.List (nub)
import Test.Hspec
import Test.QuickCheck

main = hspec $ do
  describe "duplicateCount" $ do
    it "should work for some small tests" $ do
      duplicateCount "" =?= 0
      duplicateCount "abcde" =?= 0
      duplicateCount "aabbcde" =?= 2
      duplicateCount "aaBbcde" =?= 2
      duplicateCount "Indivisibility" =?= 1
      duplicateCount "Indivisibilities" =?= 2
      duplicateCount ['a'..'z'] =?= 0
      duplicateCount (['a'..'z'] ++ ['A'..'Z']) =?= 26
    it "should work for some random lists" $ do
      property $ forAll (listOf $ elements ['a'..'z']) $ \x ->
        let xs = nub x
        in duplicateCount (concatMap (replicate 2) xs) =?= length xs
  where (=?=) = shouldBe 
```

Enter fullscreen mode Exit fullscreen mode

## 排序&分组

首先，给我们以下代码片段:

```
module Codwars.Kata.Duplicates where

duplicateCount :: String -> Int
duplicateCount = undefined 
```

Enter fullscreen mode Exit fullscreen mode

我的第一步是找出如何处理不区分大小写的问题。在`Data.Char`内是`toLower`，它可以用来映射输入`String`中的每个字符。

```
Prelude> x = "aaBbcde"
Prelude> x
"aaBbcde"
Prelude> import Data.Char
Prelude Data.Char> :t toLower
toLower :: Char -> Char
Prelude Data.Char> map toLower x
"aabbcde" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我想将相似的字符分组在一起。为此，我需要将`sort`和`group`这些字符放在一起。

```
Prelude Data.Char> import Data.List
Prelude Data.Char Data.List> :t sort
sort :: Ord a => [a] -> [a]
Prelude Data.Char Data.List> sort . map toLower $ x
"aabbcde" 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下,`sort`不会做太多事情，因为输入字符串已经被排序。不管怎样，现在我们可以用`group` :
对相似的字符进行分组

```
Prelude Data.Char Data.List> :t group
group :: Eq a => [a] -> [[a]]
Prelude Data.Char Data.List> group . sort . map toLower $ x
["aa","bb","c","d","e"] 
```

Enter fullscreen mode Exit fullscreen mode

## 首页延伸

现在，我们如何从一个长度为`[Char]`的列表到一个长度为`Int`的列表，用于过滤只出现一次的字符？`filter`号加上`length`号的`>1`条件，应该能让我们到达那里。

```
Prelude Data.Char Data.List> z = group . sort . map toLower $ x
Prelude Data.Char Data.List> filter ((>1) . length) z
["aa","bb"] 
```

Enter fullscreen mode Exit fullscreen mode

这里，`.`允许我们将`length`和`>1`组合在一起，这样两者都可以应用于提供给`filter`的`[Char]`。结果会删除在原始输入中只出现一次的任何字符的列表。

最后，我们需要输入中出现不止一个的不同字符的计数`String`，这与获取过滤列表的`length`一样简单。

```
Prelude Data.Char Data.List> f = filter ((>1) . length) z
Prelude Data.Char Data.List> length f
2 
```

Enter fullscreen mode Exit fullscreen mode

将所有这些放在一起，并将一些流水线函数分解到`where`子句中的一个变量中，我们得到了下面的`duplicateCount`函数。

```
module Codwars.Kata.Duplicates where

import Data.List (group, sort)
import Data.Char (toLower)

duplicateCount :: String -> Int
duplicateCount = length . filter ((>1) . length) . grouped
  where
    grouped = group . sort . map toLower 
```

Enter fullscreen mode Exit fullscreen mode