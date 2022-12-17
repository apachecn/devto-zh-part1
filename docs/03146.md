# 使用按位运算符

> 原文：<https://dev.to/logan/using-bitwise-operators-6c8>

我几年前在学校学到的一个很酷的东西是位运算。老实说，我几乎忘记了它们，直到有一天一位同事指出了我们代码库中的一个实现。我想谈谈我所看到的它们的一些“实际”用途。

在我们开始之前，让我们做一个快速的数学回顾。一个*操作符*是一个作用于操作数产生另一个输出的工具。著名的运营商有`+`、`-`、`*`等。它既是作用于操作数的元素或工具，也是我们称之为表示动作的东西。*操作数*是数学运算的对象。所以在`2 + 2`中，`2`是操作数，加号是运算符。

## 按位运算符简介

那么什么是按位运算符呢？嗯，一个对比特进行运算的算子！基本上，它们是在个体层次上围绕模式(如二进制数)中的位移动的运算符。因为它们在如此低的水平上运行，所以它们通常非常快。记住运算符就像`+`/加号和`-`/减号，所以它们也有数学符号*和*英文名称。不同语言的编程或数学表示会有所不同。

我将浏览并解释一些非常常见的问题。有时候我理解代码比英语更好，所以我也用 ruby 写出了逻辑*的意思*，但这是我自己的伪代码，*不是*源代码！好了，下面是主要的按位运算符:

### 不

也称为补码，该操作对每个位执行求反操作，因此`1111`将变成`0000` , `10101`将变成`01010`，以此类推。在代码中:

```
def NOT(bit)
  output = ''
  bit.each_char do |char|
    output += char == '0' ? '1' : '0'
  end
  return output
end 
```

### 和

这需要*两个*操作数并乘以每一位。所以`1111 AND 0000`是`0000` ( `1*0`跨所有位)`10101 AND 00111`是`00101`等等。

`AND`让我想起了很多语言的布尔`||`操作符，当`false || true`、`true || true`、`false || false`时，按位`AND`的行为方式是一样的，但是是二进制的。在代码中:

```
def AND(bit_1, bit_2)
  output = ''
  bit_1.split('').zip(bit_2.split('')).each do |arr|
    output += (arr[0].to_i * arr[1].to_i).to_s
  end
  return output
end 
```

### 或

这也需要*两个*操作数。`OR`采用两个位模式，如果两个位都是`0`，则产生`0`，否则产生`1`。所以，`1111 AND 0000`就是`1111`，`10101`，`00011`就是`10111`等等。

```
def OR(bit_1, bit_2)
  output = ''
  bit_1.split('').zip(bit_2.split('')).each do |arr|
    if arr[0] == '0' && arr[1] == '0'
      output += '0'
    else
      output += '1'
    end
  end
  return output
end 
```

### 异或

这是我们迄今为止最复杂的一个。`XOR`也接受*两个*操作数，如果只有*第一个*位为 1 *或*第二个位为 1，则结果为 1，但如果两个都为 0 或都为 1，则结果为 0。啊，令人困惑。让我们编码这个。

```
def XOR(bit_1, bit_2)
  output = ''
  bit_1.split('').zip(bit_2.split('')).each do |arr|
    if arr[0] == arr[1]
      output += '0'
    else
      output += '1'
    end
  end
  return output
end 
```

啊，这样好多了。

所以这些运营商都挺酷的！他们在二进制上做简单的数学运算，我们可以看到他们在理论上是如何有用的。就像我们无法想象没有乘法、加法、减法和除法的生活一样，按位运算符对计算机的生活至关重要。从这些简单的积木，我们可以做出一些非常强大的数学。

我见过的所有编程语言都有按位方法来构建它，但是它们看起来和我们在这里定义的有点不同。例如，Ruby 按位运算符:

```
NOT: ~
AND: &
OR : |
XOR: ^ 
```

在 ruby 中也有非常酷的移位器:`<<`和`>>`。这些函数将整数位向左或向右移动给定的位置数，如

```
(a >> 2).to_s(2) 
```

其中`a`为小数，`to_s(2)`表示基本上为`to_binary`，`>> 2`表示右移 2 位。`irb`的例子:

```
> a = 64
 => 64
> a.to_s(2) # the binary equivalent of a
 => "1000000"
> (a >> 2).to_s(2) # the binary equivalent of a shifted to the right
 => "10000" 
```

无论如何，我们可以在这里呆一整天，讨论移位是多么酷，但我想进入几个酷的用例，或者我看到的实际使用这些的例子。

## Tic Tac Toe

*注意:在为这篇文章做研究时，我发现了 Joris Labie 对井字游戏中使用位运算符的精彩描述:[使用位逻辑跟踪井字游戏](https://codepen.io/labiej/post/using-bit-logic-to-keep-track-of-a-tic-tac-toe-game)。请务必查看它，以获得关于如何工作的更详细的描述！*

构建井字游戏通常是新程序员被要求做的第一个入门任务之一。它经常出现在面试或代码挑战中。当我在 Ada Academy 的时候，我们对 JavaScript 的介绍是在浏览器中构建一个井字游戏。我喜欢这个项目，但我很难制定一个好方法来制定赢得比赛的逻辑。有很多不同的可能性，如何赢得 9 个不同的广场。幸运的是，当时我也是第一次学习按位运算符，并开始认为`X` s 和`O` s 与`1` s 和`0` s 并没有太大的不同。也许我可以使用按位运算符逻辑来全面跟踪组合。

我知道我需要检查每一步棋的获胜分数来判断结果，所以我写了一个方法`checkEnvironment`来调用每一轮的`winningScore`,这将决定最后一个成功的玩家是创建了一个`X` s 还是`O` s 的获胜模式

这就是`winningScore`逻辑的工作原理。它依靠一系列`binaryWins`或 8 种独特的方式获胜(3 个水平，3 个垂直，2 个对角线)。这些是非常特殊的数字，看一看它们可能有助于找出原因。

以下是获胜的二进制组合数字:

```
[111, 111000, 111000000, 1001001, 10010010, 100100100, 100010001, 1010100] 
```

用十进制表示:

```
[7, 56, 448, 73, 146, 292, 273, 84] 
```

然后让我用最后一种方式来表达它们，同样，用二进制，但是用了一些无关的`0` s:

```
[['0', '0', '0'],
 ['0', '0', '0'],
 ['1', '1', '1']],
[['0', '0', '0'],
 ['1', '1', '1'],
 ['0', '0', '0']],
[['1', '1', '1'],
 ['0', '0', '0'],
 ['0', '0', '0']],
[['0', '0', '1'],
 ['0', '0', '1'],
 ['0', '0', '1']],
[['0', '1', '0'],
 ['0', '1', '0'],
 ['0', '1', '0']],
[['1', '0', '0'],
 ['1', '0', '0'],
 ['1', '0', '0']],
[['1', '0', '0'],
 ['0', '1', '0'],
 ['0', '0', '1']],
[['0', '0', '1'],
 ['0', '1', '0'],
 ['1', '0', '0']] 
```

眼熟吗？这些二进制数字是赢的精确表达！

在这一点上，重新访问我们的按位`AND`操作符可能会有所帮助，它接受两个操作数并返回每个位的乘积。这意味着，如果你比较当前玩家拥有的任何组合，在每一个二进制获胜中，玩家组合中的任何`0`位都被抵消，变成`0`，但是任何获胜组合为`1`且玩家拥有`1`的地方都将被记录为命中。例如，看看我在这个表示为多维数组的棋盘上对角获胜(我是`X`):

```
[['X', 'O', 'X'],
 ['O', 'X', 'O'],
 ['X', 'O', 'X']] 
```

这是二进制的胜利之一:

```
[['1', '0', '0'],
 ['0', '1', '0'],
 ['0', '0', '1']] 
```

如果我们将游戏棋盘上的每一个`X`表示为 1，即使获胜的`X`多于三个，如果与我们的二进制获胜相比，它们也会被`AND`操作符抵消掉！

这太酷了！既然我们已经弄清楚了我们的逻辑，代码就很简单了(请原谅我的 javascript，它不是我的主要语言，但这很有效！):

开始时，设定我们的二元胜利:

```
binaryWins = [7, 56, 448, 73, 146, 292, 273, 84]; 
```

写出我们的`winningScore`逻辑:

```
winningScore: function(score){
  var self = this;
  for (i = 0; i < self.binaryWins.length; i += 1) {
    if ((score & self.binaryWins[i]) === self.binaryWins[i]) {
      return true;
    }
  }
  return false;
} 
```

每一次检查我们都会遍历可能的获胜，并在`AND`操作符中使用我们的`score`和`win`作为操作数，即
`score & self.binaryWins[i]`，如果发现获胜分数，则返回一个布尔值。

所以这真的很棒。我们刚刚真正使用了一个按位运算符！但井字游戏不是商业逻辑(除非你有真正酷的井字游戏业务，如果有请告诉我)。让我们探索一下使用这些光滑操作符的其他方法。

## 布尔或按位逻辑？

所以我们经常在条件句中使用布尔逻辑，但是位逻辑也一样强大！例如，这些条件通常用于在 RGB 光谱上分配颜色。我们还可以将一系列随机元素分配给不同的二进制数，并使用按位运算符进行比较。`&`和`|`做好两组有交集的比较。`^`可以用来看两组之间有什么不同。每当我们有一组关系时，就可以用按位逻辑进行比较。

## Ruby 的安全比较

这是 Ruby 中的`secure_compare`方法:

```
def secure_compare(a, b)
  return false if a.empty? || b.empty? || a.bytesize != b.bytesize
  l = a.unpack "C#{a.bytesize}"

  res = 0
  b.each_byte { |byte| res |= byte ^ l.shift }
  res == 0
end 
```

是一种超级酷的方法，可以保护我们免受计时攻击，或者攻击者可以通过响应时间来衡量网站响应的攻击。当字符串相等操作符从左到右工作(即`user_input == 'string'`)并且用户正在输入控制该逻辑的内容时，它们可以执行计时攻击。

因此为了防止这种情况，`secure_compare`取两个字符串的哈希值，然后比较它们，如下所示:

```
secure_compare(
  Digest::SHA256.hexigest('a'),
  Digest::SHA256.hexigest('b')
) 
```

如果我们看一下关于`secure_compare`的代码，我们可以看到它遍历每个字母，并在此过程中解包字节大小。然后，它迭代这些字符的字节大小，并使用`XOR`操作符来比较它们。我们可以看到，如果相应散列中的两个字节不匹配，`res`不再为零，比较返回`false`。

每个粘贴进来的散列长度相等，并且使用`XOR`来比较和查找它们的二进制等价物中的任何差异。这不仅更安全，因为它需要持续的时间来比较哈希值(无法测量不同输入的时间差)，而且速度非常快！

## 轨道位域宝石

最后，我想说一个真正酷的宝石， [`bitfields`](https://github.com/grosser/bitfields) 。它与操作符不太直接相关，但是它使用二进制逻辑在`ActiveRecord`中存储东西。

假设您有一个表，`Puppy`有多个列`has_chew_toy`、`likes_pets`和`good_walker`，它们都是布尔值。对于一个似乎主要基于布尔值的表来说，有很多真和假。如果我们可以保存一个整数*来表示所有这些字段会怎么样？我们可以！*

布尔值的组织方式如下:

```
true false false => 1 0 0 => 1
false true false => 0 1 0 => 2
false false false => 0 0 0 => 0
true true true => 1 1 1 => 5 
```

当你实例化一只新的狗时，你只需要给它赋值，比如

```
class Puppy < ApplicationRecord
  include Bitfields
  bitfield :puppy_bits, 1 => :has_chew_toy, 2 => :good_walker, 4 => :likes_pets
end

puppy = Puppy.new(has_chew_toy: true, good_walker: true)
puppy.likes_toys # true
puppy.puppy_bits # 3 
```

这是非常酷的二进制逻辑的使用！在表面之下，它使用[位运算符](https://github.com/grosser/bitfields/blob/master/lib/bitfields.rb#L171)来创建关系。

## 终注

我真的很喜欢学习按位运算符，因为它让一些看起来很可怕的东西变得清晰起来。按位运算符就像我们在小学学习的运算符一样，但是对于一个我们不熟悉的数字系统。虽然我喜欢这个实现，但是对于不太熟悉操作符的人来说，它的语法可能会很混乱，因为它看起来像布尔逻辑，但实际上是不同的。出于这个原因，我真的很喜欢有注释并有良好文档记录的解决方案，至少能识别代码库中位操作符的使用。

写这篇文章我学到了很多！如果你想更深入地探索这些操作符，我在下面留下了一些资源。

## 资源

以下是我在撰写这篇博文时使用的一些有用的资源和一些代码:

*   [Mozilla 关于按位运算符的 wiki](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)
*   [Ruby 中的按位运算符](https://www.calleerlandsson.com/rubys-bitwise-operators/)
*   [我的 javascript 井字游戏](https://github.com/loganmeetsworld/js-tic-tac-toe/tree/lm/master)
*   [Michael Hurwicz 和 Laura McCabe 在《使用 Macromedia Flash MX》中关于井字游戏的一段非常有用的随机段落](https://books.google.com/books?id=zsti09XFNbEC&pg=PA246&lpg=PA246&dq=tic+tac+toe+bitwise+operators&source=bl&ots=u5VzY_PCUx&sig=ekn1-fpmR33CnNSDD2z0xnRASOQ&hl=en&sa=X&ved=0ahUKEwjM1--yqvPWAhXH2SYKHZQ4C2QQ6AEIUjAH#v=onepage&q=tic%20tac%20toe%20bitwise%20operators&f=true)
*   [`secure_compare`文档](http://api.rubyonrails.org/v5.1/classes/ActiveSupport/SecurityUtils.html)
*   [Nick Malcolm 针对字符串比较的计时攻击](https://thisdata.com/blog/timing-attacks-against-string-comparison/)
*   [`bitfields`铁轨宝石](https://github.com/grosser/bitfields)