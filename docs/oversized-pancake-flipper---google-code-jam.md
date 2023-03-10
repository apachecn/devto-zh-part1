# 超大煎饼脚蹼-谷歌代码堵塞

> 原文：<https://dev.to/greenkey/oversized-pancake-flipper---google-code-jam>

## 谷歌代码堵塞

如果你是一名程序员，你不知道 [Google Code Jam](https://code.google.com) ，你绝对应该去看看。这基本上是一场编程竞赛，我喜欢称之为“编程奥林匹克”。

这是我参与的大约 4 或 5 年，我喜欢它，因为它挑战了我的编码技能，我不是在谈论“制作 for 循环”。

今年我想分享我对这些问题的解决方案，让我们从第一个也是最简单的开始。

## 问题

[这里是完整的问题正文](https://code.google.com/codejam/contest/3264486/dashboard#s=p0)。

你有一排煎饼和一个奇怪的鳍状肢，你必须找到正确的组合，让他们展示他们快乐的一面。

起初我认为这很简单:只要翻转不开心的煎饼，看看会发生什么。

*“这是一场比赛，解决方案不可能这么简单！”*——我内心的声音说。所以我继续做另一个问题，看看它们是否更简单，然后我回来了。

## 解

再次面对这个问题，除了第一个，我没有别的办法。我决定试一试。

你可以在这里找到代码[，但是让我解释其中的一部分。](https://gist.github.com/greenkey/1fd7a44c0b110ec9cd6f69b99b000adf)

为了简化下面的步骤，我获取输入的第一部分`line.split()[0]`，并将 pancake 行转换为二进制项列表。

```
# ... pancake_row = [p == '+' for p in line.split()[0]]
# ... 
```

列表理解类似于*“对于字符串中的每个字符，如果该字符等于`+`，则创建一个带有`True`值的项，否则为假”*
列表理解非常强大，下面是没有它的代码:

```
pancake_row = list()
for p in line.split()[0]:
    pancake_row.append(p == '+') 
```

然后我在列表中寻找要翻转的薄饼，现在每个条目都是一个布尔值，我可以用一个简单的`if`来检查它们:

```
# ... pan_size = int(line.split()[1])
# ... while i < (len(pancake_row) - pan_size + 1):
    if not pancake_row[i]:
# ... 
```

现在我把 flipper 从第一张*不开心的煎饼*开始，然后翻转。

```
pancakes: ---+-++-
flipper:  ***
flipped:  ++++-++- 
```

要在代码中创建它，我只需`not`items:`not False == True`:

```
# ... while i < (len(pancake_row) - pan_size + 1):
    if not pancake_row[i]:
        for j in range(i, i + pan_size):
            pancake_row[j] = not pancake_row[j]
# ... 
```

我想我可以使用*切片*来完成它:

```
# ... while i < (len(pancake_row) - pan_size + 1):
    if not pancake_row[i]:
        j = i + pan_size
        pancake_row[i:j] = [not p for p in pancake_row[i:j]]
# ... 
```

继续这个例子，煎饼行的演变应该如下:

```
---+-++-
***
++++-++-
    ***
+++++---
     ***
++++++++ 
```

在这个例子中，我们是幸运的，因为最后所有的煎饼都是快乐的。我们可以使用一个特殊的 Python 函数来检查它(您不必重新发明轮子):

```
# ... all(pancake_row)
# ... 
```

[T2`all()`](https://docs.python.org/2/library/functions.html#all)

> 如果 iterable 的所有元素都为真，则返回`True`

如果煎饼不是都在快乐的一边，我们可以说这是不可能的:不快乐的煎饼应该在这一排的最后，如果不翻转前面的煎饼，就不可能翻转它们。

## 我学到了什么

我的直觉是对的...我是对的！(不是我内心的声音)。

所以下一次我可以早点尝试。