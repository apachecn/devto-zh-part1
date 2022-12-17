# JavaScript 中的二进制

> 原文：<https://dev.to/gnclmorais/binary-in-javascript-2chb>

*这篇文章基于我最近在 dotJS 做的[闪电谈话](https://www.youtube.com/watch?v=Qyh_QcgotUY&hd=1)，它是为[真理子](https://twitter.com/kosamari)的[网络降临节日历](http://web.advent.today)写的。检查所有其他有趣的文章，特别是[帕姆的](http://thewebivore.com/async-await-and-why-its-great/)和[里卡多的](http://blog.ricardofilipe.com/post/light-my-house)！*

* * *

我不完全确定有多少 web 开发人员知道(或者甚至使用)它，但是 JavaScript 支持二进制。在我们最喜欢的语言中，0 和 1 可以很容易地用按位运算符来处理，这就是我在这篇文章中要展示的。

首先，*为什么？*你为什么会关心这个？在你多年的 web 开发中，你可能从未需要使用这些操作，那么你为什么还要读这篇文章呢？OMG 是不是又多了一件需要了解和添加到我的 [JavaScript 疲劳](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.d36qvkc5h)的事情？？

别担心，这只是一件好奇的事。爱怪癖的请继续看下去！这篇文章将简要介绍可用的位运算，但我已经可以向您推荐一篇来自[丹王子](https://twitter.com/_danprince)的[精彩帖子](https://danthedev.com/2015/07/25/binary-in-javascript)。简而言之，他能够大大减少我们正在开发的使用按位运算符的游戏的内存占用。他正在处理一个 512x512 像素矩阵，使用普通的旧 JavaScript 对象来表示每个像素。然而，只使用严格必要的位来保存游戏的状态，每个对象都被一个整数替换，从而减少了四倍的内存消耗！你会在他的博客中找到更多信息。

## 先说几个技术细节

让我快速地告诉您一些关于 JavaScript 如何处理数字和二元运算符的重要技术细节。

### 数字使用 64 位存储

基本上，JavaScript 中的所有数字都是浮点数。一位表示符号(0 表示正数，1 表示负数)，11 位表示点的位置，最后 52 位表示数字的实际位数。

```
 sign | exponent | fraction
(1 bit) | (11 bit) | (52 bit)
     63 | 62 -- 52 | 51 --- 0 
```

Enter fullscreen mode Exit fullscreen mode

### 超过 32 位的数字被截断

这意味着，从你在前一段读到的 64 位中，我们将只保留右边的 32 位(即最低有效位)。

```
 // 15872588537857
Before: 11100110111110100000000000000110000000000001
After: 10100000000000000110000000000001
                                   // 2684379137

var a = (-5 >>> 0).toString(2);
// "11111111111111111111111111111011"
parseInt(a, 2);
// 4294967291 
```

Enter fullscreen mode Exit fullscreen mode

### 对比特对进行逐位运算

通过将第一个操作数中的每个位与第二个操作数中的相应位配对来执行运算。示例:

```
// Using only eight bits here for illustration purposes:
var a = 9; // 0000 1001
var b = 5; // 0000 0101

a & b -> a // 0000 1001
              &&&& &&&&
         b // 0000 0101
              ---------
              0000 0001 -> 1 (base 10) 
```

Enter fullscreen mode Exit fullscreen mode

## 按位运算符

JavaScript 有七个按位运算符，它们都将操作数转换为 32 位数字。

### `&`(和)

```
| a | b | a & b |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 0 | 
| 1 | 0 | 0 |
| 1 | 1 | 1 | 
```

Enter fullscreen mode Exit fullscreen mode

简单地说，如果至少有一个`0`，那么`&`会导致`0`。

### `|`(或)

```
| a | b | a | b |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 | 
```

Enter fullscreen mode Exit fullscreen mode

在`|`的情况下，如果至少有一个`1`，输出将是`1`。

### `^`(异或)

```
| a | b | a ^ b |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 | 
```

Enter fullscreen mode Exit fullscreen mode

不同的位会导致`1`，简单来说。我必须承认 XOR 是我最喜欢的，它可能相当令人困惑。谁知道下面的代码是做什么的，就给谁 10 分:

```
var a = 1, b = 2;
a ^= b; b ^= a; a ^= b; // wat? 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有得到它，不要担心，你不是一个人。这是一个非常混乱的**值交换，没有第三个变量**(虽然只是在整数之间)。看看这个:

```
var a = 1; // 0001
var b = 2; // 0010

a ^= b; // 0001 ^ 0010 = 0011
b ^= a; // 0010 ^ 0011 = 0001
a ^= b; // 0011 ^ 0001 = 0010

console.log(a); // 2 (0010)
console.log(b); // 1 (0001) 
```

Enter fullscreen mode Exit fullscreen mode

[![Mind. Blown.](img/372c6be0d2db45a93151acdf0484ce98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kRTu-1jV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://mrwgifs.com/wp-content/uploads/2013/11/Finns-Mind-Is-Blown-In-Space-On-Adventure-Time.gif)

### `~`(非)

运算符简单地反转所有的位，包括符号。这就像反转图像的颜色。

```
 9 = 00000000000000000000000000001001
     --------------------------------
~9 = 11111111111111111111111111110110 = -10 (base 10) 
```

Enter fullscreen mode Exit fullscreen mode

将`~`应用于任意数量 x 会产生-(x + 1)。在上面的例子中，~9 产生-10。这与 JavaScript 使用[二进制补码](https://en.wikipedia.org/wiki/Two%27s_complement)表示 32 位数字的方式有关(这里我们不会详细讨论)。

### `<<`(左移)

`<<`将 0 位从右边的**推向左边的**，从左边丢弃的数量与从右边丢弃的数量一样多。

```
9 : 0000 0000 1001
9 << 2 : 0000 0010 0100 // 36
                     ^^
                     new bits 
```

Enter fullscreen mode Exit fullscreen mode

### `>>`(符号传播)右移

`>>`向右移位，但它并不简单地称为*右移*，因为与左移不同，它并不总是推 0。推送的位取决于数字的符号:如果数字为正，则推送 0 位；如果数字是负数，将使用 1 位来代替。

```
 9 : 0000 0000 1001
 9 >> 2 : 0000 0000 0010 // 2
          ^^
          new bits

-9 : 1111 1111 0111
-9 >> 2 : 1111 1111 1101
          ^^
          new bits 
```

Enter fullscreen mode Exit fullscreen mode

### `>>>`(补零)右移

`>>>`是右移的一个特例，从左向右的新位总是 0，与数字的符号无关。它的一个结果是把任何负数变成正数。

```
 9 : 0000 0000 1001
 9 >>> 2 : 0000 0000 0010
           ^^
           new bits

-9 : 1111 1111 0111
-9 >>> 2 : 0011 1111 1101
           ^^
           new bits 
```

Enter fullscreen mode Exit fullscreen mode

## 有趣的按位运算符

那么，我们能对这些运营商做些什么呢？鉴于他们的怪癖和行为，让我们来看看一些古怪的行为。许多这些怪癖源于从 64 位到 32 位的转换。

### 截断数字

```
var a = 3.14;
var b = -3.14;
console.log(a & a, b & b); // 3, -3
console.log(a | 0, b | 0); // 3, -3
console.log( ~~a,~~ b); // 3, -3 
```

Enter fullscreen mode Exit fullscreen mode

### 将字符串转换为数字，仿效`parseInt`

```
var a = '15' >>> 0;
var b = '15.4' >>> 0;
console.log(a, b); // 15, 15

var c = '3.14';
var d = c | 0;
var e = c & c;
console.log(d, e); // 3, 3 
```

Enter fullscreen mode Exit fullscreen mode

### 将一个数乘以 2 的倍数

```
console.log(7 << 1); // 7 * 2 * 1 = 14
console.log(7 << 2); // 7 * 2 * 2 = 28
console.log(7 << 3); // 7 * 2 * 3 = 56
// … 
```

Enter fullscreen mode Exit fullscreen mode

### 不同子串搜索

```
var string = 'javacript';
var substr = 'java';

// If the sub-string is found,
// appying NOT to the index will return a negative number,
// which is a truthy value;
// If not found, `indexOf` will return -1,
// which in turn ~(-1) == 0, into the `else` case.
if (~string.indexOf(substr)) {
  // Found the sub-string!
} else {
  // Nope, no match
} 
```

Enter fullscreen mode Exit fullscreen mode

## 那么……应该用这个吗？

简答…号

长答…看情况。正如您所看到的，在使用这个工具时，人们需要注意许多陷阱和怪癖。你需要知道你正在处理的变量类型，这在动态类型语言中很难做到，比如 JavaScript。你不会希望意外地截断带小数的数字，或者将负数变成正数。

你应该考虑的另一个问题是当你决定写`x << 1`或者`x * 2`时随之而来的代码混淆。然而，这可能是您愿意做的一个妥协，使用像[微小二进制格式](https://github.com/danprince/tiny-binary-format)这样的包装器，这变得非常容易管理。

最后，记住[道格拉斯·克洛克福特不喜欢它](http://stackoverflow.com/questions/1908492/unsigned-integer-in-javascript/1909320#1909320)，认为它是 JavaScript 不好的部分之一。

**然而**，对于那些你需要从你正在工作的硬件中获取更多的额外项目或应用，为什么不呢？我写 JavaScript 是为了我个人项目的乐趣，在这些情况下，我喜欢做与日常工作不同的事情。如果这涉及到左移右移&，对你有好处！让你的代码保持怪异和有趣——并在这个过程中学习一些东西。