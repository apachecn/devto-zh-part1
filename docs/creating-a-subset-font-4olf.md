# 创建子集字体

> 原文：<https://dev.to/michaelherold/creating-a-subset-font-4olf>

在网页上使用自定义字体会带来几个潜在的问题。最常见的是，这些问题表现为两种类型的问题之一:可怕的“无样式文本闪现”(FOUT)或“不可见文本闪现”(FOIT)；或者由于阻止对外部服务的调用中指定的字体而导致的较差的初始呈现时间。通过在关键渲染路径中仅放置一个子集字体，可以减少 FOUT/字体的数量，并加快初始渲染性能。

然而，我发现任何地方都没有描述子集字体的创建。这篇文章讨论了我是如何创建子集字体的，我使用的工具，以及一些关于你应该在你的字体中添加什么子集的想法。

* * *

扎克·莱瑟曼([@扎克里亚特](https://twitter.com/zachleat))最近和[谈了很多](http://www.zachleat.com/web/web-font-data-uris/) [关于字体加载策略](http://www.zachleat.com/web/preload/)。虽然我理解了帖子的理论，但在试图找出如何实现它时，我遇到了一个非常简单的问题:我不知道如何创建子集字体。我想试试他的“人造文字闪光”的想法，但我不知道如何创建一个子集字体。

我花了一段时间研究它，想出了一个制作方法。这篇文章介绍了我使用的方法和一些背景知识，比如你在设置字体子集时到底做了什么。

### 什么是字体？

字体本质上是由地址指定的字形列表。如果你熟悉 ASCII 表，你可能记得字母 *A* 对应数字 *45* 。也就是说， *A* 位于*地址* *45* 。然后，字体创建者将该列表编码为一种字体格式:

*   嵌入式 OpenType (EOT)
*   OpenType 字体(OTF)
*   TrueType Font (TTF)
*   Web 开放字体(WOFF)
*   Web 开放字体 2 (WOFF2)

从本质上讲，这些格式都只是一个地址和符号表。有一些细节，比如反锯齿提示和连字，可以以不同的格式使用，但我感兴趣的是表格。

### 什么是子集字体？

像巴厘岛语中的 Noto Sans 这样的小型字母表的字体可能只有大约 47 个字形，但是如果您需要一个具有完整的中日韩(CJK)字形集的字体，您可能会看到成百上千个字形。

子集字体试图避免这种情况，它只包含显示页面上最重要内容所必需的字形。如果你正在写一个英语网站，你可以只包括英语字母和阿拉伯数字:总共 62 个字符。

子集字体的最终文件大小将比完整字体小得多。目前在这个网站上，我使用了 Noto Sans 字体的子集作为我网站的初始渲染。WOFF 文件格式的子集字体在磁盘上是 7.4KB，而完整字体是 190KB。子集字体节省了 96.1%。

### 如何创建一个？

有很多工具(开源的和闭源的)可以用来处理字体。我用来创建子集字体的是一个叫做 [fonttools](https://github.com/behdad/fonttools) 的 Python 库。它有几个用于操作字体文件的命令行实用程序。`pyftsubset`是子集化和优化字体的工具。

这个工具有无数的选项。为了简洁起见，我只介绍我在创建子集字体时使用的选项。

我想花点时间提一下，你应该只使用你有权限修改的字体。在本文的例子中，我修改了 Google 的 Noto Sans 字体，使用的是 SIL 开放字体许可证。这个许可证允许修改和再分发，很像麻省理工学院的开源代码许可证。

接下来，我列出我使用的命令，然后讨论选项。

```
$ pyftsubset NotoSans-Regular.ttf \
    --unicodes="U+0020,U+0041-005A,U+0061-007A" \
    --layout-features="" \
    --flavor="woff" \
    --output-file="NotoSansSubset.woff" 
```

Enter fullscreen mode Exit fullscreen mode

首先，`NotoSans-Regular.ttf`文件是我开始使用的字体文件。

接下来，我使用`--unicodes`选项指定包含在子集字体中的字形。我只包括拉丁字母(即用英语书写时使用的标准字母)和空格字符。这些对应于以下 Unicode 地址:

*   `U+0020`是标准空间
*   都是大写字母
*   `U+0061-007A`是小写字母

之后，我使用`--layout-features`选项列出了我在字体中包含的布局特征。这些东西就像连字，以文件大小为代价使字体更易读。我在这里犯了一个大罪，由于我打算如何使用字体，我没有选择任何一个。因为我希望页面只将这个子集字体显示为“虚假文本的闪光”,所以我觉得省略细节是可以的。

因为它的相对空间效率和[在我能使用](http://caniuse.com/#feat=woff)上 88.13%的广泛可用性，我使用`--flavor`选项选择 WOFF 作为字体的文件格式。子集工具只能输出 WOFF 或 WOFF2，但如果需要，您可以将结果文件导入另一个工具以获得 TrueType 或 OpenType 字体。

最后，我使用`--output-file`选项将结果文件保存为`NotoSansSubset.woff`。

### 有哪些权衡？

子集字体为您提供了一个较小的下载文件，以便在下载完整字体之前显示页面上的大多数重要文本。这意味着你必须在字体大小和功能之间进行平衡。

在理想情况下，您应该提供只包含您在第一页上使用的字形的 web 字体，然后依赖于完整的 web 字体下载。然而，如果你想提供一个静态网页，这就变得很乏味，因为你必须为你网站上的每个页面创建一个子集字体。这意味着在一个页面上包含比您需要的更多的字形可能会更好，以覆盖更广泛的页面集；基本上，以更大的文件大小为代价来增加字形数量。

另一方面，你实际上是强迫你的访问者下载两次字形集:一次是子集字体，一次是完整的 web 字体。这是平衡行为的关键。您不希望提供不必要的字形，因为您希望减少网络上的重复数据量。

我认为一个实际的解决方案是创建一个子集字体，大致遵循[帕累托原则](https://en.wikipedia.org/wiki/Pareto_principle):在第一次渲染时覆盖 80%的内容。我认为当书写英语时，一个好的子集字体将只包括字母表(或者可能是字母表和数字)。

只包含字母表允许网站访问者在最早的时候开始阅读。他或她可能会注意到一些遗漏的标点符号或数字，但是即使没有这些，你的页面仍然是可以理解的。你可能不同意，所以用不同的规则选择你自己的子集！

### 总结起来

现在你知道什么是子集字体，以及如何创建一个。子集字体在概念上很简单，但是如何创建它还不是很清楚。通过使用像`fonttools`这样的工具，你可以很容易地选择你自己的字体。

子集字体是你的网页性能策略中的一个小优化。通过沿着网站的关键渲染路径发送少量字形，您可以缩短首次渲染的时间，并通过使用子集字体显示“虚假文本闪烁”来避免“不可见文本闪烁”或“无样式文本闪烁”的问题。

然而，子集字体只是这项技术的一部分。这样做需要一些客户端逻辑来进行字体替换。本系列的下一篇文章将讨论我从 Zach Leatherman 的文章中学到的策略。

创建子集字体的帖子最早出现在《T2》杂志上。