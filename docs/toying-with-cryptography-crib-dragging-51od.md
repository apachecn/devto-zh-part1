# 玩弄密码学:拖动婴儿床

> 原文：<https://dev.to/samwho/toying-with-cryptography-crib-dragging-51od>

在过去的几年里，随着英国政府想要对它做一些令人讨厌的事情，密码已经成为英国的热门话题。这也是我最薄弱的地方之一。我不太了解它是如何工作的，也许我应该了解。正因为如此，我在读 [crypto101.io](https://crypto101.io) 。这是由 [lvh](https://twitter.com/lvh) 写的一本书，免费提供。

书中的教学方法正是我喜欢学习的东西。它从一开始就按时间顺序展开，展示了每种方法是如何被破解的，以及当时是如何解决的，一直到现代密码。

这一切都始于一个理想:一次性垫。

先决知识:我将使用 Ruby 来演示一些东西。不需要密码学知识。

## 简介

我从事这项工作时，对事物如何加密和解密一无所知。事实证明这很简单，一切都围绕着简单的位运算 XOR。

XOR 运算通常用^(脱字符号)表示，工作方式如下:

```
0 ^ 0 = 0
1 ^ 0 = 1
0 ^ 1 = 1
1 ^ 1 = 0 
```

因此，如果两位不同，产生的结果是 1。我们来看一些更复杂的例子:

```
0110 ^ 1111 = 1001
1001 ^ 1111 = 0110
0000 ^ 1000 = 1000
1111 ^ 1111 = 0000 
```

在这些例子中，XOR 有一些有趣的性质。

*   如果你对某物本身进行异或运算，你得到 0。
*   如果你对 0 做异或运算，你得到的是它本身。
*   上面两条规则揭示了 XOR 是“可交换的”，这意味着不管你把参数放在什么顺序，你都会得到相同的结果。其他交换运算的例子有乘法和加法。非交换运算的一个例子是除法。

如果你把两个东西异或在一起，然后把结果和其中一个异或，你会得到另一个。更正式:

```
A ^ B = C
C ^ B = A
C ^ A = B 
```

这些特性在创建和破解加密系统时非常重要。

## 一次性垫

这是最简单也是理论上最安全的传递信息的方式，除了预定的接收者，任何人都不能阅读。事情是这样的:

*   鲍勃想给爱丽丝发一条信息。
*   Bob 接受了他的消息，并生成了一个与消息长度相同的随机位串。这是“一次性密码本”，但我称之为“钥匙”。
*   鲍勃将他的信息与密钥进行异或运算。
*   Bob 以不可拦截的方式将密钥交给了 Alice。
*   然后鲍勃可以自由地以他想要的任何方式将加密的消息发送给爱丽丝。
*   然后，Alice 将加密的消息与密钥进行 xor 运算，并恢复原始消息。

如果密钥是真正随机的，安全地交换，并且只使用一次，这个系统提供了所谓的“完美的保密”，并且除了强力攻击之外，还能抵抗其他攻击。

### 然而…

一次性填充是极其不切实际的，在现实中也没有使用。

*   你必须与预定的接收者安全地交换你的密钥，这与首先想要传输安全消息提出了完全相同的问题。
*   你需要一个新的密钥来存放你要发送的每封邮件。
*   您的密钥必须和您的数据一样大。
*   如果您多次使用该密钥，恢复密钥和原始消息将变得非常容易。

## 从多条消息中导出密钥

假设您有两条消息，并且您怀疑发送方使用相同的密钥通过 XOR 对两条消息进行加密。记住我们上面的两条规则:

```
A ^ A = 0
A ^ B = B ^ A 
```

使用这两个属性，我们可以得到用同一个密钥加密两条消息的乐趣:

```
cipher1 = msg1 ^ key
cipher2 = msg2 ^ key
cipher1 ^ cipher2 = (msg1 ^ key) ^ (msg2 ^ key)
cipher1 ^ cipher2 = msg1 ^ msg2 ^ key ^ key
cipher1 ^ cipher2 = msg1 ^ msg2 ^ 0
cipher1 ^ cipher2 = msg1 ^ msg2 
```

对密文进行异或运算会得到与对原始消息进行异或运算相同的结果。为什么这很重要？记住上面的另一条规则:

> 如果你把两个东西异或在一起，然后把结果和其中一个异或，你会得到另一个。

如果我知道交换的消息是英文的，我就可以猜出常见的英文单词，并与 XORd 密码文本进行 XOR 运算，从而可能揭示原始消息的部分内容。这就是所谓的“婴儿床拖动”。你在密文上“拖动”一组普通的字符，希望能揭示原始信息。

通过文本来解释有点令人困惑，所以让我们举个例子。我需要几个实用函数来帮我完成这个。首先，对两个字符串进行异或运算的函数:

```
def xorstr(str1, str2)
  result = ""
  str1.bytes.each_with_index do |byte, i|
    result << (byte ^ str2[i].ord).chr
  end
  result
end 
```

和一个使婴儿床拖动更容易的功能:

```
def cribdrag(cipher, word)
  cipher.chars.each_cons(word.length) { |cons| yield xorstr(word, cons.join) }
end 
```

因此，让我们打开一个 IRB 会话，并加密一些东西！

```
irb> msg1 = "The cat sat on the mat."
=> "The cat sat on the mat."
irb> pad = File.read("/dev/urandom", msg.length)
=> "|A\xC8\xD9\xE1\x9EF2\xE2\xAE\x99\xBE\xD4\xC2kcV\n\xC2t\xEE(\x8C" 
```

敏锐的观察者会注意到我已经从`/dev/urandom`那里得到了我的密钥，这不是一种加密安全的生成随机数的方式。你是正确的，但是为了举例，而不是耗尽我的熵机器，我将使用一个伪随机密钥。

```
irb> cipher1 = xorstr(msg1, key)
=> "()\xAD\xF9\x82\xFF2\x12\x91\xCF\xED\x9E\xBB\xACK\x17>o\xE2\x19\x8F\\\xA2" 
```

现在我们有了一条加密信息。在这一点上，我们都很好，我们可以安全地交换密钥，并以我们喜欢的方式发送消息，世界将继续转动，彩虹仍会出现。让我们毁了这一切。

```
irb> msg2 = "Hello, cruel old world."
=> "Hello, cruel old world."
irb> cipher2 = xorstr(msg2, key)
=> "4$\xA4\xB5\x8E\xB2fQ\x90\xDB\xFC\xD2\xF4\xAD\a\av}\xAD\x06\x82L\xA2" 
```

请注意，消息必须长度相同，或者以某种方式填充，才能重用密钥。让我们看看数学是否成立，密码 XORd 在一起是否与消息 XORd 在一起相同:

```
irb> xorcipher = xorstr(cipher1, cipher2)
=> "\u001C\r\tL\fMTC\u0001\u0014\u0011LO\u0001L\u0010H\u0012O\u001F\r\u0010\u0000"
irb> xormsg = xorstr(msg1, msg2)
=> "\u001C\r\tL\fMTC\u0001\u0014\u0011LO\u0001L\u0010H\u0012O\u001F\r\u0010\u0000" 
```

万岁。没有骑士，没有末日，数学仍然有效。

接下来，我们将尝试一点婴儿床拖动。我们的`cribdrag`函数接受一个加密的字符串和第二个字符串，然后将第二个字符串滑过第一个字符串，生成加密字符串 XORd 的一部分。

在英语文本中，最常见的是单词“the”被空格包围。所以我们要试一下:

```
irb> cribdrag(xorcipher, " the ") { |chunk| puts chunk } 
```

并且输出:

```
<ya),
-}$im
)8d(t
lx%1c
,9<&!
m +d4
t7iq1
cu|tl
!`y)o
4e$*!
18'dl
l;i)0
ou$uh
!8x-2
ld wo
0<z*?
hf'z-
2;wh0
okeu 
```

很多胡言乱语，但有两件事包含了所有的英文字母，其中一个在我所知道的任何单词中都没有出现:

```
ld wo
okeu 
```

现在，我将尝试获取一个以“ld”结尾或以“wo”开头的常用单词列表，并尝试拖动这些单词。为了加快速度，我将直接使用“世界”:

```
irb> cribdrag(xorcipher, "world") { |chunk| puts chunk }

kb{ h
zf>`)
~#~!0
;c?8'
{"&/e
:;1mp
#,sxu
4nf}(
v{c +
c~>#e
f#=m(
; s t
8n>|,
v#b$v
:~+
g'`#{
?}=si
e mat
8|d 
```

其中只有一个似乎是合法的，" e mat "，我知道它的开头是" th "。所以现在我们有了“垫子”,它很可能是信息的一部分。已经三岁了，我能猜到“坐在垫子上的猫”很可能在某个地方被发现:

```
irb> cribdrag(xorcipher, "cat sat on the mat") { |chunk| puts chunk }

}l}, cnz18'dl})f
nh8,>57!l;i)0%s;
j-xm'"u4~"ou$u.k
/m9t0``1#!!8x-2"~y
o, cruel old world
.57!gp8on"0<z*?`qt 
```

在那里，我们可以看到底线附近的大部分原始信息。有趣的是，对于您恢复的消息的每一小段，您都可以通过将恢复的消息块与原始密文进行异或运算来计算出用于加密它们的密钥的相应位:

```
irb> xorstr(cipher1[4..-2], "cat sat on the mat")
=> "\xE1\x9EF2\xE2\xAE\x99\xBE\xD4\xC2kcV\n\xC2t\xEE("
irb> xorstr(cipher2[4..-2], "cat sat on the mat")
=> "\xED\xD3\x12q\xE3\xBA\x88\xF2\x9B\xC3's\x1E\x18\x8Dk\xE38"
irb> xorstr(cipher1[4..-2], "o, cruel old world")
=> "\xED\xD3\x12q\xE3\xBA\x88\xF2\x9B\xC3's\x1E\x18\x8Dk\xE38"
irb> xorstr(cipher2[4..-2], "o, cruel old world")
=> "\xE1\x9EF2\xE2\xAE\x99\xBE\xD4\xC2kcV\n\xC2t\xEE(" 
```

所以我们知道这两个字符串中的一个是密钥的一部分，如果我们有第三个消息要解密，我们可以检查这两个，看看我们得到了什么，但是现在我将通过展示密钥的一部分确实是这些字符串中的一个来满足你的好奇心:

```
irb> key[4..-2]
=> "\xE1\x9EF2\xE2\xAE\x99\xBE\xD4\xC2kcV\n\xC2t\xEE(" 
```

## 自动化这个过程

所以我们刚才做的是非常手动和复杂的，它可以自动化吗？

实现自动化的困难在于，当我们看到人类可读的文本时，如何识别它们。我们不能保证从拖拽中得到完整的单词。我最近在火车上尝试的一个想法是使用一种熵的度量来给透露的文本“打分”:

```
def entropy(str)
  str.chars.each_cons(2).inject(0) do |total, (a, b)|
    total += (a.ord - b.ord).abs
  end / str.length.to_f
end 
```

是的，我知道代码很可怕。我在火车上。它所做的是计算字符串中每个字节的平均差值，然后除以字符串的长度。所以最大值是 255，最小值是 0。其思想是，英文字母就其字节值而言是靠在一起的(低熵)，随机的无意义应该是相当分散的(高熵)。让我们看看它是如何支撑的:

```
irb> cribdrag(xorcipher, " the ") { |chunk| puts "#{chunk} #{entropy(chunk)}" }

<ya), 28.8
-}$im 48.4
)8d(t 39.0
lx%1c 31.4
,9<&! 8.6
m +d4 38.6
t7iq1 36.6
cu|tl 8.2
!`y)o 47.6
4e$*! 25.8
18'dl 18.6
l;i)0 33.2
ou$uh 36.2
!8x-2 33.4
ld wo 34.2
0<z*? 35.0
hf'z- 45.0
2;wh0 28.0
okeu 22.2 
```

嗯。不太好。一些胡言乱语得分很低，而实际答案得分很高。我们试试别的:

```
irb> cribdrag(xorcipher, "cat sat on") { |chunk| puts "#{chunk} #{entropy(chunk)}" }

l}l , cnz 25.7
nh8 , >57! 21.1
j-xm'"u4~" 53.6
/m9t0``1#! 35.2
o, cruel o 34.2
.57!gp8on" 29.8
7"u4b-;!#~ 40.1
 ``1?.ul & 33.0
buel<`80'| 26.8
wp8or-dh}! 36.2
r-;!?q<2 q 35.1
/.ulc)fopc 23.2
,`80;s;?b~ 29.0
b-dha.k- n 39.2 
```

同样，没有实际意义。看起来这个方法是行不通的。至少部分问题是空格(字节值 32)离字母相当远，像“a”(字节值 97)，这就把平均值拉高了。如果我们有一个去掉空格的熵函数会怎么样？

```
def entropy(str)
  str.gsub(' ', '').chars.each_cons(2).inject(0) do |total, (a, b)|
    total += (a.ord - b.ord).abs
  end / str.length.to_f
end

irb> cribdrag(xorcipher, " the ") { |chunk| puts "#{chunk}  #{entropy(chunk)}" }

<ya), 28.8
-}$im 48.4
)8d(t 39.0
lx%1c 31.4
,9<&! 8.6
m +d4 34.2
t7iq1 36.6
cu|tl 8.2
!`y)o 47.6
4e$*! 25.8
18'dl 18.6
l;i)0 33.2
ou$uh 36.2
!8x-2 33.4
ld wo 7.0
0<z*? 35.0
hf'z- 45.0
2;wh0 28.0
okeu 5.2 
```

看起来更好…另一个例子呢？

```
irb> cribdrag(xorcipher, "cat sat on") { |chunk| puts "#{chunk} #{entropy(chunk)}" }

l}l , cnz 23.3
nh8,>57!{ 21.1
j-xm'"u4~" 53.6
/m9t0``1#! 35.2
o, cruel o 16.6
.57!gp8on" 29.8
7"u4b-;!#~ 40.1
 ``1?.ul& 26.6
buel<`80'| 26.8
wp8or-dh}! 36.2
r-;!?q<2 q 31.5
/.ulc)fopc 23.2
,`80;s;?b~ 29.0
b-dha.k- n 39.2 
```

好一点了。最低的那个是正确答案。也许通过更多的恶作剧，我们可以用一套精心制作的婴儿床实现这一工作，并自动完成发现过程，还可能进行一些检查，看看空格之间的东西是否是真实的单词。不过，现在我要把这个切掉。

## 包装完毕

Cryptograhpy 似乎没有我最初想象的那么可怕。这本书我已经看了四分之一，有些东西还是有意义的。随着我们的深入，密码变得越来越复杂，但所涉及的问题都很有意义，解决方案似乎也很合理(直到我阅读下一章，作者展示了它们是如何破解的)。

更快！:)