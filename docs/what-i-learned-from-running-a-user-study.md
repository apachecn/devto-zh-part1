# 我从用户研究中学到了什么

> 原文：<https://dev.to/etcwilde/what-i-learned-from-running-a-user-study>

用户研究。不可避免的邪恶。它们很费时间，很难做对，有时很讨厌，最糟糕的是，我们不得不与人类打交道。但是...我们还是要做。我们可以拥有一个拥有世界上所有测试和文档的软件，但是如果它不能可验证地帮助人们，那还有什么意义呢？仅仅拥有大量用户并不意味着人们喜欢你的程序，他们可能只是需要使用它。用户测试验证了人们(点击链接的真正奇特的脚本不是人)能够真正使用你的程序并理解它。

这些是我在对一个[工具](http://li.turingmachine.org)运行我的第一个比较研究后收集的一些想法，这个工具测试了我为可视化 git 库而构建的[模型](https://dev.to/etcwilde/merge-trees-visualizing-git-repositories)。

## 提问

提出符合你目标的问题实际上非常困难。我遇到了两个主要问题，一是测试我想要什么，二是让参与者清楚他们想做什么。

我们如何补救？用户研究的用户研究。基本上，进行试点研究是个好主意。在这个试点研究中，您需要运行 3 到 5 个用户。这些参与者应该与计划的预期用户来自相同的人口统计数据，显然，与实际参与您的研究的人来自相同的人口统计数据。你不希望试点有太多的人，否则你会浪费时间，更重要的是，你会浪费参与者。您不能在实际的用户研究中使用试点研究的参与者。

不要仅仅停留在使用试点研究测试问题上。实际收集结果，然后使用这些数据来确定您想要运行哪种分析。

主要有两种类型的问题:定量和定性。虽然学术软件工程论文开始将两者混为一谈，但评论者似乎对定量有强烈的偏好(我没有引用，所以这只是传闻)。定量研究更容易，至少有两个原因；首先，这些数字是具体的，不容争辩(假设你在进行研究时没有作弊)，其次，答案是一个数字，一旦你开始分析，就很容易比较。

数字只能说明问题的一半。他们说的是“什么”，而不是“为什么”。但是很难具体描述“为什么”。你的参与者可能会有很多原因来解释他们为什么做某事，即使结果“是什么”是一样的，所以你可能需要更多的参与者。另一个问题，它在你的论文中占了更多的篇幅。

现在，你已经想好了你的问题，他们要做的任务，一切都准备好了。有一种偏差，源于按一定的顺序做事。你需要将事情随机化，以确保这种情况不会发生。我使用了一个简短的 python 脚本来处理这个问题。我会为每个参与者运行这个脚本，它会生成一个脚本让我读给参与者听。它随机化了工具的使用顺序和任务的顺序。只要我正确地阅读脚本，研究就会正确地进行。

## 收藏

我用的是屏幕+音频捕捉。这被证明是非常好的，原因有很多。它有一个缺点，我不得不坐在那里观看每一部电影，从中提取关键信息。其余的都很好。

首先，它让这项研究感觉不像是一项研究，而更像是一次对话。你的环境可能会感觉异常干净，所以这已经让你的参与者感到不安。如果你坐在那里记笔记，玩计时器，同时观察他们的一举一动，那是没有用的。他们看不到视频记录(尽管他们是通过同意书被告知的)，所以感觉不像是被检查的小白鼠，而更像是工具的实际使用者。

一旦视频收集完毕，我就可以坐下来观看，并从中提取信息。如果我第一次错过了什么，我可以回头再看一遍。我也可以从视频中获得更多的上下文，而手写笔记只能记录你写下的内容。此外，我不必大惊小怪的计时器，持续时间是正确的视频。

## 储存

至少从我交谈过的人看来，CSV 文件是一种流行的数据存储方式。我觉得他们疯了。CSV 文件易于编写脚本和进行数据输入。这就是它们的用处所在。我最终使用了 sqlite 数据库。有些人可能会认为，对于存储像用户研究这样小的东西来说，完整的关系数据库有点大材小用。他们是对的，但他们也是错的。是的，我不需要 ACID 或 transactions，或者其他任何东西；就这一点而言，他们完全正确。那么，关系数据库给了我什么，而简单的 CSV 文件却没有呢？灵活性。如果我想查看数据中的不同指标，我不需要编写另一个脚本来分析它，我只需在数据库中进行查询。当您创建 CSV 文件时，您并不确切知道您将在数据中寻找什么。CSV 文件对于实验来说太可怕了。您可能需要实现关系数据库中的大多数特性来操作 CSV 文件，那么为什么不直接使用数据库来省去麻烦呢？

当我坐在那里做数百条 INSERT 语句将数据添加到数据库时，一些人笑了。当他们询问数据的各个方面时，他们不再笑了，并且通过一个简单的查询就能够在几秒钟内操纵数据来准确地显示我想要的东西。

你不需要一个完整的数据库。这就是为什么 sqlite 是一个很好的选择。它很简单，足够快，并且它将数据放入一个文件中，而不是硬盘上某个看似隐藏的位置。最后一点非常有用，因为一旦研究在参与者同意书中的某个地方完成，您可能需要了解如何以安全的方式销毁数据。

## 分析

在深入探讨这个问题之前，默认情况下我是 vim 用户，但是 emacs 中的 org-mode 特性完全破坏了 vim 的功能，emacs 确实是这里的最佳选择。

组织模式。这是一个很好的研究工具。这就像类固醇的降价。Vim 试图有一个组织模式，但它与实际的组织模式相差甚远，几乎不能称自己为同一事物。我的大多数 emacs 配置都存在于一个 org 文件中。

那么如果是 markdown，为什么有利于分析呢？因为您可以直接从文档中运行代码，并将结果直接放在代码下面。这使得你的研究完全可以复制。如果任何人对用于分析的方法有任何疑问，可以向 org-mode 文件提出。它包含您所做的查询，以及这些查询的结果。这对于完全可重复和可验证的研究来说是非常好的。

一个简单的例子；

```
#+name: results
#+begin_src sqlite :db ./data/data.db :colnames yes
SELECT question, tool, avg(time), count(*) total, count(CASE WHEN correct = 1 THEN 1 END) correct
FROM results;
#+end_src 
```

Enter fullscreen mode Exit fullscreen mode

| 问题 | 工具 | 平均(时间) | 总数 | 正确的 |
| --- | --- | --- | --- | --- |
| one | one | One point two five | Ten | seven |
| one | Two | Four point five | Ten | Two |
| Two | one | Zero point two | Ten | eight |
| Two | Two | Nine point one | Ten | one |

在块内点击 C-C-C-C 将直接在文档中创建表格。Github 甚至会很好地呈现这一点，语法突出显示查询，并使表格看起来很漂亮。

此外，对于分析数字数据非常有用的 R 脚本也可以以类似的方式运行，而且由于 emacs 可以呈现 pdf，因此可以生成直接嵌入到文档中的图像。一旦你习惯了 emacs，它会让你的工作流程变得非常快速和轻松。

r 脚本在执行统计分析和生成图表方面非常出色。使用它。你可以像普通编程语言一样用 R 编程，带有条件 if 和循环。这部分并不特别，但是扩展包使它特别。它有大部分的统计测试，你可以应用你想要的。结果将为您提供测试统计数据和 P 值。可以给 R 提供 RSQLite 数据库后端，可以直接连接数据库。没有 CSV 文件可以从外部操作，只查询您感兴趣的数据并使用它进行分析。非常干净，非常容易，完全可重复。

## 重述

这些是我从第一次用户研究中学到的东西。有些是艰难的，有些似乎天生就很清楚，而且效果很好(确认偏差，没错，但确实有效)。

所以快速回顾一下

*   在实际使用之前，最好(完全)测试一下这个测试，以确保它能正常工作。
*   使用脚本编写研究脚本。
*   屏幕截图+音频是收集结果的好方法。
*   将结果保存在实际的 dbms 中，这有助于分析。
*   emacs + org-mode + R + database 让最后的分析变得简单。

让我知道你学到了什么，我会列出一些想法，让用户研究尽可能的简单。