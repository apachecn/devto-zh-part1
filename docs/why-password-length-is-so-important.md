# 为什么密码长度如此重要

> 原文：<https://dev.to/dean/why-password-length-is-so-important>

如果我告诉你如何制作一个安全 400，000 倍且更容易记忆的密码，会怎么样？让我给你介绍一下密码。你可能从无数人那里听说过这句话。XKCD 谈论**密码短语**以及它们如何比密码好得多。它解释了为什么密码更好，但并没有真正进入数学到底有多简单。我们不会在这里谈论任何关于熵的东西，因为如果你懂数学的话，那是没用的。我们将讨论暴力破解密码和字典攻击，并制作一些快速脚本来计算密码与密码短语的安全性。

# 短而复杂的密码

假设你有一个密码，大约 12 个字符。它有数字和符号。这意味着，假设你受限于键盘上的字符，26(小写)+ 26(大写)+ 10(数字)+ 10(数字+ shift) + 10(额外符号)+ 10(额外符号+ shift)。这给我们带来了 92 个可能的字符，为了方便计算，我们取 100 个字符。

对于单个字符的密码，有 100 种可能。两个字符等于 10000 + 100 = 10100。我们中的许多人都是程序员，所以让我们写一个快速的 python 程序来描述长度为`length`或更短的密码可以有多少种可能性。

```
>>> def password(length):
...   if length < 0:
...     return 0
...   return 100**length + password(length-1) 
```

Enter fullscreen mode Exit fullscreen mode

并且只是为了测试一下(注意，该程序也允许空密码，所以它要大 1)

```
>>> password(1)
101
>>> password(2)
10101 
```

Enter fullscreen mode Exit fullscreen mode

那么 12 个字符的密码怎么样？让我们试着把它放到我们的函数里。

```
>>> password(12)
1010101010101010101010101 
```

Enter fullscreen mode Exit fullscreen mode

这是一个巨大的数字！这比十亿亿亿多一点。但是让我们试着用一种不同的方法来解决这个问题...

# 长而简单的密码

现在，让我们来讨论一个密码短语，而不是密码。密码短语比密码长得多，也简单得多，而且更难猜测。当然，每个密码都是由单词组成的。为了便于记忆，我们甚至不会在其中包含任何数字或符号。只有 26 个字母、空格键、连字符和下划线。我们总共有 29 个角色。

让我们创建一个新的 python 函数来描述相同的模式，但是针对密码短语。

```
>>> def passphrase(length):
...   if length < 0:
...     return 0
...   return 29**length + passphrase(length-1) 
```

Enter fullscreen mode Exit fullscreen mode

给它几个测试值。

```
>>> passphrase(1)
30
>>> passphrase(2)
942 
```

Enter fullscreen mode Exit fullscreen mode

那么密码有多难被暴力破解呢？好吧，假设我们的密码是“平均句子的长度是多少”，它是 42 个字符。让我们把它放进我们的函数里。

```
>>> passphrase(42)
27287005492884718602602534825004439917772077566939113764846271 
```

Enter fullscreen mode Exit fullscreen mode

那个数字非常大。根据 python 的说法，大约比可能的 12 个字符密码的数量大`2.7e+37`倍！另一方面，这确实假设他们通过暴力破解密码，尝试随机字母直到出现正确的序列。让我们来看看字典攻击，而不是暴力攻击。

字典攻击一次尝试单词而不是字母。这意味着他们更擅长破解密码。这个短语中最罕见的词是“长度”(排名 1151)，和“句子”(排名 4675) [(来源)](https://github.com/first20hours/google-10000-english/blob/master/google-10000-english-no-swears.txt#L1154)。所以，假设我们的攻击者正在寻找 5000 个最常见的英语单词。

所以，当然，我们来做一个函数来描述这个！

```
>>> def passphrase_dictionary(words):
...   if words < 0:
...     return 0
...   return 5000**words + passphrase_dictionary(words-1) 
```

Enter fullscreen mode Exit fullscreen mode

并测试它

```
>>> passphrase_dictionary(1)
5001
>>> passphrase_dictionary(2)
25005001 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们看看一个不超过 8 个单词的密码有多少种可能性。

```
>>> passphrase_dictionary(8)
390703140628125625125025005001 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，正如预期的那样，这比暴力破解要少得多。但是与原始值相比...

```
>>> passphrase_dictionary(8) / password(12)
386796.10922184435 
```

Enter fullscreen mode Exit fullscreen mode

我们的密码比我们的 12 个字符的密码更容易记忆，并且安全近 40 万倍。此外，在密码中选择不常用的单词会大大提高密码的安全性！我使用的密码短语中的单词都相当常见，尽管它仍然比随机生成的 12 个字符的密码更安全。

# 结论

密码短语比密码更容易记忆，同时也更安全。当然，它们会花更长的时间来打字，但是你也是在输入真正的单词，而不需要不停地去按那个`shift`键。本例中使用的(非常)简单、易记的密码比随机生成的 12 个字符的密码安全 400，000 倍。唯一阻止我们到处使用密码的是字符限制，这有时会强加在网站上，在这种情况下，你可能需要使用一个普通的旧密码。但是如果没有限制，就用一个漂亮的长密码。