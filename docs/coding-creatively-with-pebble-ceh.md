# 创造性地编码...卵石？

> 原文：<https://dev.to/jvarness/coding-creatively-with-pebble-ceh>

我向你们坦白一件事...去年我不写 Java 代码的时候...我在为我的卵石时代写看板。事实上，我非常喜欢为我的 Pebble 编写 watchfaces，以至于我为一个编程竞赛创建了一个提交的[文件](https://github.com/jvarness/cerner-2-to-the-5th/tree/master/C)就是 watchface！

我知道你们很多人可能会想:

*   自从 Fitbit 收购了 Pebble 之后，pebble 就不再是一家公司了。
*   智能手表是一种日渐式微的时尚。
*   你的 Pebble 过了今年就没用了。

虽然所有这些想法都有其价值，但 Pebble 将永远在我心中占据特殊的位置，原因只有一个:

**Pebble watchfaces 让我能够创造性地编写代码！**

我的旅程首先从尝试编写一个[模拟表盘](https://apps.getpebble.com/en_US/application/56b67e70f17b7944f0000041)开始。我只想能够在表盘上画箭头，所以我画了！虽然我喜欢[编写代码](https://github.com/jvarness/arrowhead)，但这个 watchface 有很多地方我不喜欢:

*   很难读懂，因为我没有在手表上做任何标记来告诉你是哪个小时。
*   很难找到好看的颜色组合。用户可以为时针、分针和背景选择 3 种不同的颜色，只有 64 种颜色可供选择。
*   它带有一个电池指示器，但如果你不阅读描述，它就有点模糊，而且它也不准确。
*   它使用了手表自带的默认字体之一，所以它看起来不是很独特，除了一半时间都很难阅读这该死的东西。
*   其他表盘做了一些很棒的事情，比如告诉你温度、步数和日期，所有这些箭头都没有做到。

我把所有这些挫折都发泄在我的下一个手表上，我称之为[会话](https://github.com/jvarness/sessions)。这款手表允许你配置两种不同的颜色，而不是三种，它向你显示步数和天气，它使用了一种很酷的字体，叫做龙虾，我从谷歌字体下载的。这款腕表更受[欢迎](https://apps.getpebble.com/en_US/application/570da5e6b175cfd32d000007?section=watchfaces)，我为自己感到非常自豪！

在 Pebble 第三次成功启动 Kickstarter 的前夕，我写了一个简单得多的 watchface，名为 [Kota](https://apps.getpebble.com/en_US/application/5802492988277fe07a0000bf?section=watchfaces) ，上面有我从网上下载的另一种字体和一个电池指示器。这款 watchface 最终变得简单了很多，无论是在用户体验上，还是在编写的[上。](https://github.com/jvarness/kota)

我们现在距离 Pebble 被 Fitbit 收购还有一年左右的时间，我真的很怀念写 watchfaces 的日子。事实上，只需要几百行代码和很少的测试就能表达我自己，这一直很吸引我。我喜欢每个表盘都是不同的，每个表盘都有自己独特的方式向用户传达不同的信息，无论用户理解起来多么模糊或清晰。

我也喜欢能够用 C 写代码，不像我大学的大多数 C 作业那样糟糕(我错过了关于`malloc`、`calloc`和`realloc`的课程，我不擅长指针)。

你们为 Pebble 写过东西吗？其他可穿戴设备呢？在开发这些东西的过程中，你最喜欢的时刻是什么？