# 挑战-印刷螺旋

> 原文：<https://dev.to/heikodudzus/challenge---print-spiral-6kg>

## 一级

用你选择的语言写一个程序，显示 n × n 个数字的“螺旋”。
例如，下面是 n = 10 时螺旋的样子:

```
*Main> printSpiral 10
99 98 97 96 95 94 93 92 91 90
64 63 62 61 60 59 58 57 56 89
65 36 35 34 33 32 31 30 55 88
66 37 16 15 14 13 12 29 54 87
67 38 17  4  3  2 11 28 53 86
68 39 18  5  0  1 10 27 52 85
69 40 19  6  7  8  9 26 51 84
70 41 20 21 22 23 24 25 50 83
71 42 43 44 45 46 47 48 49 82
72 73 74 75 76 77 78 79 80 81
*Main> 
```

Enter fullscreen mode Exit fullscreen mode

## 二级

确保你的程序使用恒定(或线性)空间。这意味着，不允许在打印之前构建一个数组(或者用 O( n^2)构建另一个消耗空间的数据结构)。

在这里找到的想法:[https://www . quora . com/What-is-some-interest-puzzles-asked-in-computer-science-programming-technical-visities](https://www.quora.com/What-are-some-interesting-puzzles-asked-in-computer-science-programming-technical-interviews)

请在下面的评论中发表你的解决方案。

## 澄清

对于 O(n)中的解，允许建立一个字符串(或数组，...)进行单行，但仍然没有构建一个包含*螺旋中包含的所有*信息的数组。也许这更适合您选择的语言，或者它有利于构建原型，...埃文·阿曼，谢谢你的关心。