# 浏览一本书

> 原文：<https://dev.to/liginv/skimming-through-a-book-a5o>

我选择玛丽·雪莱的《弗兰肯斯坦》来测试我的 UNIX 技能。我听说过，但没有读过。我从古腾堡项目网站下载了一份纯文本的 UTF-8。检查文件类型，给我这个:

```
Ligin% file frankenstein.txt
frankenstein.txt: UTF-8 Unicode (with BOM) text, with CRLF line terminators

```

该文件包含古腾堡项目许可证的几个段落和文件开头和结尾的其他内容；删除它&使用另一个名为 [**dos2unix**](https://www.lifewire.com/dos2unix-linux-command-4091910) 的程序，我将文件转换成 ASCII 文本。

```
Ligin% dos2unix frankenstein.txt
dos2unix: converting file frankenstein.txt to Unix format...
Ligin% file frankenstein.txt
frankenstein.txt: ASCII text

```

* * *

从字数统计( **wc** )命令开始，我得到了以下结果:

```
Ligin% wc frankenstein.txt
7243 74952 421503 frankenstein.txt

```

全书包含 7243 **行**，74952 **字**，421503 **字**。

字数让我们对书的类型有一个大致的了解。一部典型的**小说**有 4 万字以上，一部**中篇小说**有 17500 到 39999 字，一部**中篇小说**有 7500 到 17499 字&一部**短篇小说**有 7500 字以下。由于这本书有 70，000 多字，我们可以认为这是一本小说。

为了进一步分析的一致性，我将所有字符转换成小写字母，并将每个单词拆分成一行

```
Ligin% tr 'A-Z' 'a-z'< frankenstein.txt| tr -sc 'a-z' '\n'
```

现在更深入地探索这本书的内容，通过应用**排序** & **uniq** 程序，我们可以找到最常用的单词和它出现的次数。

```
Ligin% tr 'A-Z' 'a-z' < frankenstein.txt| tr -sc 'a-z' '\n'| sort | uniq -c | sort

```

似乎**“**”是使用最多的词，出现了 4195 次，但这并没有给本书带来任何启示。总之，我把它保存到一个名为 frank.words 的文件中，没有进行排序。

```
Ligin% tr 'A-Z' 'a-z' < frankenstein.txt|tr -sc 'a-z' '\n'> frank.words

```

现在使用 grep 我可以在不同长度的单词中找到最常用的单词。

```
Ligin% grep -w "[a-z]\{12\}" frank.words | sort -nr | more

```

当我改变数值时，我发现了一些有趣的词&它的频率。

```
2850 i
1391 a
1776 my
867 me
608 he
136 myself
134 father
71 friend
45 horror
39 months
36 geneva
34 spirit
59 clerval
55 justine
54 friends
51 cottage
76 feelings
44 creature
38 thoughts
27 murderer
92 elizabeth
65 miserable
37 mountains
21 vengeance
34 discovered
32 sensations
39 countenance
28 endeavoured
27 frankenstein
18 conversation
14 wretchedness
14 tranquillity
14 circumstances
7 disappointment
6 notwithstanding

```

最长的单词有 16 个字母(有趣的事实；) ).

通过将两个相邻的单词排列成一个序列，我们可以分析单词之间不同类型的频繁分布，这被称为[二元模型](https://en.wikipedia.org/wiki/Bigram)。

现在通过创建一个新文件 frank.nextwords，它将单词存储在前一个文件 frank.words 之上，

```
Ligin% tail +2 frank.words > frank.nextwords

```

通过粘贴和排序这两个文件，我们可以形成一个 bigram 文件 frank.bigram。

```
Ligin% paste frank.words frank.nextwords| sort | uniq -c > frank.bigram

```

现在对新的 bigram 文件使用 sort，

```
Ligin% sort -nr frank.bigram | more

```

现在一些有趣的事情开始出现了。

```
228 i was
219 i had
100 as i
90 my father
49 my heart
46 my eyes
41 the cottage
36 i thought
35 my mind
31 my friend
31 my dear

```

目前，我认为这是书上说的:

它以第一人称的视角讲述(可能是弗兰肯斯坦的)，更像是日记。这个人和他/她的父亲很亲近。其他一些字符是伊丽莎白，贾丝汀，克莱瓦尔。这个人和他的朋友也很亲近。其中涉及到一间小屋，大概是人物居住的地方。有人被指控犯有谋杀罪，周围弥漫着某种恐怖气氛，这也使得人们有理由说这可能是一部推理小说。

为了知道我有多接近，我需要坐下来读它，因为它涉及恐怖和谋杀，我最好不要在睡觉前读它；)

再见