# 神奇的 0xC2

> 原文：<https://dev.to/koehr/the-magic-0xc2>

*这是来自[我的个人博客](https://log.koehr.in/2017/04/09/the-magic-0xc2)的交叉帖子*

我构建了一个具有文件上传功能的 web 应用程序。前面几个 Vue.js 后面一个 CouchDB。一切都应该非常简单明了。

但是…

当我上传图像文件时，它们不知何故被损坏了。上传的文件比原来的大，新的“文件格式”是不可读的。我很好奇。这些文件会发生什么？这些变化看起来非常随机，但却是可重复的，所以我创建了几个测试文件来看看到底什么时候发生了变化。

我的第一个文件是这样的:

```
0123456789
ABCDEFGHIJKLMNOPQRSTUVWXYZ
abcdefghijklmnopqrstuvwxyz 
```

令我惊讶的是，文件保持不变！我的好奇心增长了。与此同时，我在上传的 hexdump 中发现了一个非常有趣的模式:C3 BF C3。到处都是。在另一份文件中，我发现了与 C2 相似的模式。所以我写了下一个测试文件。这次是二进制文件:

```
00 01 02 03 04 05 06 07  08 09 10 11 12 13 14 15 |................|
16 17 18 19 20 21 22 23  24 25 26 27 28 29 30 31 |.... !"#$%&'()01|
32 33 34 35 36 37 38 39  40 41 42 43 44 45 46 47 |23456789@ABCDEFG|
48 49 50 51 52 53 54 55  56 57 58 59 60 61 62 63 |HIPQRSTUVWXY`abc|
64 65 66 67 68 69 70 71  72 73 74 75 76 77 78 79 |defghipqrstuvwxy|
80 81 82 83 84 85 86 87  88 89 90 91 92 93 94 95 |................|
96 97 98 99 a0 a1 a2 a3  a4 a5 a6 a7 a8 a9 aa ab |................|
ac ad ae af b0 b1 b2 b3  b4 b5 b6 b7 b8 b9 ba bb |................|
00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................| 
```

编辑:你可能已经注意到了，我是以 10 进制计数的，但实际上是 16 进制。所以我跳过了 A-F 直到达到 A0。这可能看起来很奇怪，但不影响测试。

上传后的结果是

```
00 01 02 03 04 05 06 07  08 09 10 11 12 13 14 15  |................|
16 17 18 19 20 21 22 23  24 25 26 27 28 29 30 31  |.... !"#$%&'()01|
32 33 34 35 36 37 38 39  40 41 42 43 44 45 46 47  |23456789@ABCDEFG|
48 49 50 51 52 53 54 55  56 57 58 59 60 61 62 63  |HIPQRSTUVWXY`abc|
64 65 66 67 68 69 70 71  72 73 74 75 76 77 78 79  |defghipqrstuvwxy|
c2 80 c2 81 c2 82 c2 83  c2 84 c2 85 c2 86 c2 87  |................|
c2 88 c2 89 c2 90 c2 91  c2 92 c2 93 c2 94 c2 95  |................|
c2 96 c2 97 c2 98 c2 99  c2 a0 c2 a1 c2 a2 c2 a3  |................|
c2 a4 c2 a5 c2 a6 c2 a7  c2 a8 c2 a9 c2 aa c2 ab  |................|
c2 ac c2 ad c2 ae c2 af  c2 b0 c2 b1 c2 b2 c2 b3  |................|
c2 b4 c2 b5 c2 b6 c2 b7  c2 b8 c2 b9 c2 ba c2 bb  |................|
00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................| 
```

又来了:神奇的 0xC2！

因此，所有值为`0x80`或更高的字节都以`0xC2`为前缀。`0x7F`是最初的 7 位 ASCII 码中最后一个可能的值——这就是我眼中的 UTF 8 编码！

在 UTF 8 中，`0x7F`之后的所有字符至少有两个字节长。它们以`0xC2`为前缀，直到`0xC2BF`(这是一个倒置的问号)，然后是`0xC380`。所以，事情是这样的，在去服务器的路上，文件被编码成 UTF-8 \_(ツ)_/格式

**编辑:**在[黑客新闻](https://news.ycombinator.com/item?id=14089827)上发表了一些评论后改正了一些错误