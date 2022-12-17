# 清理代码:抽象重用的代码

> 原文：<https://dev.to/aarohmankad/cleaning-up-your-code-abstracting-reused-code-4mea>

这是我在 Medium 上的文章[“清理你的代码”的摘录。我将每天发布每个部分，在媒体上提前阅读！](https://medium.com/acm-ucr/cleaning-up-your-code-8c2a88cb47fc)

如果你在你的代码库中发现了大量重复的代码或者依赖于相同的逻辑，它们可能是抽象的有力竞争者。

将代码抽象成单独的函数或类有助于降低代码的复杂性。如果与该代码块相关的算法发生变化，您只需在一个地方进行更改，而不是在整个代码库中的多个地方进行更改。

一个好的抽象看起来像什么？好吧，让我们以之前的[为例。`great_names.cpp`文件真的很好看！然而，如果我们想要找到一个基于不同的`FLAG_CODE`或不同的`gameBoard`的单元格，我们就必须重写这个代码块。因此，让我们将代码转移到一个独立的函数中。](https://dev.to/aarohmankad/cleaning-up-your-code-good-names-2e0o)