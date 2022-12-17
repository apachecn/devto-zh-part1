# 异或的三种方式

> 原文：<https://dev.to/horia141/the-three-ways-of-xor-2ela>

我们都知道异或运算。它是 NOT，AND 和 OR 的表亲。像 AND 和 OR 一样，是二元布尔函数。与它们不同，大多数编程语言没有明确的“逻辑操作符”，而是只显示了 bitewise 版本。不管怎样，密码学家喜欢它，机器学习的人害怕它。而且理由很充分。尽管它很简单，但还是有很多有趣的事情要说，本文介绍了其中的三个。

首先，XOR 看起来像这样，以真值表的形式:

| a | b | 异或 |
| --- | --- | --- |
| Zero | Zero | Zero |
| Zero | one | one |
| one | Zero | one |
| one | one | Zero |

*异或*的第一种方式是当两个自变量不同时为真，相同时为假。因此，套用逻辑 OR 的描述，当`a`为真或`b`为真时，`a XOR b`为真，但不是两者都为真。这是编写程序逻辑时最常见的理解。XOR 并不等同于 NOT、AND 和 OR，而是作为布尔运算的逻辑运算符，但是您可以使用`!=`达到几乎相同的效果，但是不会强制使用许多语言中的布尔运算符。然而这并不是一个很大的损失。XOR 不能很好地处理短路，如果期望 XOR 至少保证一个块的一次执行，它会导致代码中的错误。

*XOR*的第二种方式是，它是一个受控 NOT。第一个参数控制第二个参数是否被否定。这是密码术中最常见的理解，其中 XOR 作为密码的基本构造块出现。密钥用来控制否定，而明文就是被否定的东西。它在电路设计中也是一个很好的工具。还要注意 XOR 是对称的。根据我们的需要，论点可以互换角色。

*XOR 的第三种方式*是它是最“复杂”的二元布尔函数。有 2 个<sup>{ 2<sup>{ 2 } }</sup>/T8】/sup>= 16 个不同的两个运算符的布尔函数。其中一半是对另一个函数的否定，那么我们就把这 8 个截然不同的列举出来。

| 名字 | 00 | 01 | Ten | Eleven | 熵 | Sep？ |
| --- | --- | --- | --- | --- | --- | --- |
| 零 | Zero | Zero | Zero | Zero | Zero | 是 |
| 和 | Zero | Zero | Zero | one | Zero point eight one | 是 |
| 答！b | Zero | Zero | one | Zero | Zero point eight one | 是 |
| a | Zero | Zero | one | one | One | 是 |
| ！腹肌 | Zero | one | Zero | Zero | Zero point eight one | 是 |
| b | Zero | one | Zero | one | One | 是 |
| 异或运算 | Zero | one | one | Zero | One | *否* |
| 或者 | Zero | one | one | one | Zero point eight one | 是 |

我们可以看到 XOR 在集合中看起来有点奇怪。虽然还有其他两个 0 和两个 1 的函数，但它们都只是其中一个参数的副本。从信息论的角度来看，它在集合中具有最高的[熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))(与具有 2 个 1 的其他函数一起)。再者，从几何和机器学习的角度来看，函数不是[线性可分的](https://en.wikipedia.org/wiki/Linear_separability)。这是唯一一个不是这样的。事实上，[T5 和例子](http://www.ece.utep.edu/research/webfuzzy/docs/kk-thesis/kk-thesis-html/node19.html)被用来对抗[感知机](https://en.wikipedia.org/wiki/Perceptron)以突出它们的局限性。~~这也是 80 年代后期艾冬天[的种子之一。正如评论者@dvhwgumby 和@JamesPaulWhite 所指出的，人工智能的冬天是一个独立于神经网络失宠的事件。这两个事件之间有 10-15 年的时间。虽然两者都是人工智能民间传说的一部分，但在这里使用因果关系是不正确的。](https://en.wikipedia.org/wiki/AI_winter)~~

一些额外的待遇:异或可以被认为是一个不平等的功能。当输入数为偶数时，输出 0，为奇数时，输出 1。也是加法器的[求和部分](https://news.ycombinator.com/item?id=13631562)，也就是没有进位部分。它也是一个函数，当任何一个输入改变时，它的[输出也随之改变，这不能用 AND 和 OR 来说。同样，从`a XOR b = c`开始，知道两个变量中的任何一个，我们可以得到第三个。这在密码学中也很有用，可以通过 XOR 对加密的东西进行解密。在 XOR 和 Clifford 代数的外积之间也有一个对应关系](https://www.reddit.com/r/programming/comments/5tnst2/the_three_ways_of_xor/ddnvtic/)

对于这样一个小功能来说，这是一个很大的打击。

*编辑*:帖子上的大量评论，尤其是关于 XOR 在编程语言中以`!=`的形式出现。径直走进了那间。我也更新了熵计算和人工智能冬天的部分。我还添加了很多其他的异或方式，这些都是从[黑客新闻](https://news.ycombinator.com/item?id=13630376)和 [Reddit](https://www.reddit.com/r/programming/comments/5tnst2/the_three_ways_of_xor/) 的评论中获得的。

* * *

[1]不幸的是，我理解了句子中的每个单词，但不是整个句子。我的数学不是很好。