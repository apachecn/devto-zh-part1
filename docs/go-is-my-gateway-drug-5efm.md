# 围棋是我的入门药

> 原文：<https://dev.to/peteraba/go-is-my-gateway-drug-5efm>

这篇文章最初出现在我的博客上。

从 2013 年我第一次开始玩 Go 的时候，我已经是一名很长时间的 PHP / JavaScript 开发人员了。到现在已经快 5 年了，从那以后我一直是这门语言的倡导者。我是许多尝试并发原语的程序员之一，但因为它的简单性而留下了。有了 Go，我才意识到没有继承和 OOP shenenigans 的其他东西，生活是多么美好。另一方面，围棋也给了我尝试更多语言的动力。它成了我学习编程语言的入门药。

当然，我以前玩过 Java、Python 或 Ruby 之类的语言，但老实说，它们并没有给我提供比我以前更多的东西。当时我对编写游戏、桌面或移动应用程序不感兴趣，我很乐意做 webdeveloper。对我来说，Java 感觉太笨拙，Ruby 感觉太神奇，Python 感觉缺乏动力。(更不用说 2.x vs 3.x 的困惑了。)学习这两种语言中的任何一种都觉得为了潜在的收益付出了太多的工作。《围棋》则不同，实际上是一见钟情。它太容易学了，而且承诺(和实现)了很多，以至于我甚至不能停止学习更多。但是一旦我掌握了它，我意识到我的旧 OOP 语言感觉冗长，我的动态类型语言感觉脆弱。然而，我觉得 Python 比 Go 更好读，这让我看了更多的语言，大部分是像 Rust 和 Elixir 这样的新语言。

有几种方法可以尝试新的语言，我通常喜欢用它们做一个小项目，即使我从来没有真正完成那些项目。然而，在过去的两周，我又回到了以前最喜欢的地方。这项服务的真正好处在于，你可以查看其他人是如何解决某个特定问题的，而且只要付出一点额外的努力，就有可能找到或构建一个“完美”的解决方案。

为了展示我的意思，我将在整个练习中为一个叫做汉明问题的简单问题添加几个解决方案。这个问题就是找到两个字符串之间的“距离”,这意味着字符的数量不同。

### PHP

以下是我 2015 年的 PHP 解决方案:

```
<?php

function distance($a, $b)
{
    if (strlen($a) != strlen($b)) {
        throw new \InvalidArgumentException('DNA strands must be of equal length.');
    }

    $aSplit = str_split($a);
    $bSplit = str_split($b);

    $diff = array_diff_assoc($aSplit, $bSplit);

    return count($diff);
} 
```

实际上很简单，只有一个 if 来检查字符串长度。另一方面，PHP 已经显示出一些奇怪的东西:

1.  注意 str_split 有一个' _ '字符来分隔各个部分，但是 strlen 是作为一个单词写的？典型的 PHP...
2.  注意，array_diff_assoc 是一个相当特殊的函数。它向我展示了有一个非常大的标准库要学习。
3.  抛出异常是可以的。如果您是这个函数的消费者，您最好能捕捉到这里抛出的异常。
4.  缺少类型意味着你可以用任何你想要的值调用这个函数，从`null`到任何类型的`object`，由你决定。(是的，这个可以在 PHP 7.0+中可选修复，种。)

#### 注 1。

希望没有人会试图在一行中完成所有的事情，因为这会严重伤害任何看着它的人

```
<?php

function distance($a, $b)
{
    if (strlen($a) != strlen($b)) {
        throw new \InvalidArgumentException('DNA strands must be of equal length.');
    }

    return count(array_diff_assoc(str_split($a), str_split($b)));
} 
```

#### 注 2。

你也可以用 PHP 7 写下面的代码来解决一些问题。打字仍然是可选的，尽管如此，仍然没有一般的保证。考虑到它是一种脚本语言，除非你的测试套件已经覆盖了你，否则在你部署它们之前，刹车变化是不必要明显的。

```
<?php

/**
 * @throws \InvalidArgumentException
 */
function distance(string $a, string $b): string
{
    if (strlen($a) != strlen($b)) {
        throw new \InvalidArgumentException('DNA strands must be of equal length.');
    }

    $aSplit = str_split($a);
    $bSplit = str_split($b);

    $diff = array_diff_assoc($aSplit, $bSplit);

    return count($diff);
} 
```

### 去吧

我的 2017 年围棋解决方案:

```
package hamming

import "errors"

const testVersion = 6

func Distance(a, b string) (int, error) {
    if len(a) != len(b) {
        return -1, errors.New("Lengths of the provided strings are not equal.")
    }

    aBytes := []byte(a)
    bBytes := []byte(b)

    dist := 0
    for i, ch := range aBytes {
        ch2 := bBytes[i]
        if ch != ch2 {
            dist += 1
        }
    }

    return dist, nil
} 
```

好的，这里有什么外卖？

1.  这比 PHP 解决方案长得多，也复杂得多。
2.  另一方面，它需要用户更少的先验知识来理解，我们甚至不使用标准库。(公平地说，在 PHP 中也可以避免)
3.  这个解决方案不可能在不完全破坏可读性的情况下写在一行中。
4.  注意强类型是如何使得用奇怪的数据调用这个方法变得不可能，以及返回的数据是如何可预测的，除了错误也可能是一个`nil`。
5.  注意错误是如何通过强制返回错误类型来处理的，“从不”抛出意外的东西。(实际上可能会出现恐慌，但其用法与典型的异常不同)
6.  一定要明白错误可以是`nil`。*警告:*会有龙！
7.  奇怪的是，函数以大写字母开头，而其他标识符以小写字母开头。这使得代码在 Go 中公开/导出。

*注:*我试图找到一个更实用或更简单的解决方案，但我没有找到。

Benjamin Cable 指出了上述代码的一些问题，并提出了一个更好的替代方案。对于所描述的问题，请查看评论部分，代码如下:

```
func distance(a, b []byte) (dist int, err error) {
    if len(a) != len(b) {
        return dist, errors.New("length of provided strings is not equal")
    }

    for i := 0; i < len(a); i++ {
        if a[i] != b[i] {
            dist++
        }
    }

    return dist, nil
} 
```

### 巨蟒

2015 年我的 Python 解决方案:

```
"""Python distance exercise solution"""

def distance(text_one, text_two):
    """Calculate distance between two strings"""
    count = 0

    for (char1, char2) in zip(text_one, text_two):
        if char1 != char2:
            count += 1

    return count 
```

外卖:

1.  即使没有错误检查，这里的圈复杂度也比 PHP 大一点，但是我发现它更容易阅读。
2.  Python 对元组和`zip`函数的支持使它有点像函数，尽管`for`循环在 FP 中并不常见。
3.  没有什么可以阻止我们用奇怪的数据调用这个函数。
4.  我们几乎不能期望从这个函数返回什么。如果我们将返回类型改为字符串，调用者在收到运行时错误之前不会知道。
5.  我们在这里使用标准库，但是`zip`比`array_diff_assoc`对于 PHP 来说感觉不那么深奥。

*注意:*我的 Python 大致处于高级初学者水平，Christoph Schindler 似乎更有经验，他的解决方案读起来更像函数:

```
def distance(sequence, other):
    if len(sequence) != len(other):
        raise ValueError('Sequences must be of same length.')

    return sum(1 for a, b in zip(sequence, other) if a!=b) 
```

附加外卖:

1.  通过引发一个值错误，我们可以在调用者一方造成更大的破坏。

### F-锐

最后，这是我这周用 F#写的同样的东西，有两天的经验:

```
module Hamming

let distance (strand1: string) (strand2: string): int option =
    match strand1.Length, strand2.Length with
    | x, y when x = y ->
        let seq1, seq2 = (List.ofSeq strand1), (List.ofSeq strand2)
        Some(List.fold2 (fun acc a b -> if a = b then acc; else acc + 1) 0 seq1 seq2)
    | _, _ -> None 
```

我们能从这里带走什么？

1.  它比 Python 解决方案稍长。
2.  你可能会发现`match`的语法很奇怪，但是现在你可以把它看作是类固醇的`if`。
3.  你不可能用错误的类型调用这个函数。
4.  这个响应甚至比 Go 的更明显，因为`null`部分在这里并不奇怪，它是由`option`类型注释表达的。
5.  我们在这里也使用了标准库，但是很像 PHP 解决方案中的`zip`、`List.fold2`和`List.ofSeq`也没有`array_diff_assoc`那么深奥。

从上面的例子中并不明显，但是显式类型定义在 F#中是可选的，除非编译器告诉你应该提供它们。然而，类型不是可选的，不像 PHP 和 Python 那样是可选的，因为它是一种编译语言，所以出错的空间也很小。例如，与 C#相比，不必首先处理类型可以使编码 feek 更像一种脚本语言。我只希望编译器快一点，如果可能的话，快得多。(Go 的编译器感觉快了 100 倍左右。)

更新:正如我上面提到的，我刚刚开始深入 FP 语言和 F#，这里有一个来自评论的更好的解决方案:

```
let distance s1 s2 = 
  if String.length s1 <> String.length s2 then None
  else
    Seq.zip s1 s2
    |> Seq.sumBy (fun (c1, c2) -> if c1 <> c2 then 1 else 0)
    |> Some 
```

很难说这是一个非常优雅的解决方案。

### 结论

我想我发现 F#解决方案是这里最好的并不奇怪，但是当然我并不是说 F#是四种语言中最好的。我甚至觉得笼统地说这样的话不太公平。我也不认为这个例子以任何方式证明 FP 优于 OOP。显然，这些例子没有很好地揭示这些语言有多难学，它们在野外有多有用，以及这些语言的许多其他重要方面。然而，至少对我来说，它们证明了学习其他语言是值得的，因为并非所有的语言都是平等的。

此外,[exercim . io](http://exercism.io)是一个令人敬畏的经销商。