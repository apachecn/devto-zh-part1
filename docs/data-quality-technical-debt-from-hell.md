# 数据质量:来自地狱的技术债务

> 原文：<https://dev.to/m1pko/data-quality-technical-debt-from-hell>

*本帖最初发表于[这里](https://partitionbyblog.wordpress.com/2017/09/24/data-quality-technical-debt-from-hell/)。*

大多数人可能都知道，技术债务有多种形式。数据质量只是其中一种形式。

数据质量是大型组织中的一个主要问题，在大型组织中，多个系统、应用程序和服务相互交互，交换和更改数据。不连贯总是会发生的。要么是因为有人犯了一个错误，要么是因为某个地方有一个未识别的 bug，要么是因为系统的架构不像它应该的那样健壮，或者仅仅是因为人们喜欢忽略它(相信我，最后一个错误发生的次数比它应该发生的次数多得多！).这将有助于你的技术债务的持续增长，有时是平静但稳定的增长。

不要让自己被愚弄。人们很容易开始忽视数据质量，尤其是当你在一个数据迁移项目上工作了几个月的时候，即使你不愿意，你也会开始厌倦并犯错误。见鬼，有时候你只是过了糟糕的一天…这可能发生在我们任何人身上！

有几种方法可以解决这个问题。以下是一些方法:

1.  让我们从陈述显而易见的事情开始。确保您的服务尽可能不容易出错。这是所有技术债务应该开始解决的地方:甚至在它有机会存在之前。我知道我是一个梦想家，不切实际或什么的，但在一个完美的世界里，这就是它应该如何工作。
2.  当你有一个团队致力于执行数据分析和不同系统之间的比较，并执行数据修复以纠正数据时，这很好；这个动作应该尽可能经常地定期执行。
3.  理解是什么导致了不连贯；这可能很难实现，但当你设法做到这一点，根除它的概率将非常高。这一切看起来很棒，实现起来也相对简单，但随之而来的是任何组织都存在的另一个普遍问题:人。我们是自己最大的敌人，大多数情况下，当我们试图解决一些共同的问题时，却走向了相反的方向。幸运的是，这并不总是发生，随着时间的推移，趋势变得完全相反，尽管仍然有很大的改进余地(总是有！).

写这篇文章的想法是发现贯穿 4 次系统升级和迁移的数据不一致的直接结果，这种不一致最终困扰了 16 年后首次创建有缺陷数据的人(在这个特殊情况下是我)。噩梦，你不觉得吗？你呢？

*图片来源:T a k([https://www.flickr.com/photos/takashi/18862634/](https://www.flickr.com/photos/takashi/18862634/))via[visual hunt](https://visualhunt.com/re/cfdb23)/[CC BY-NC](http://creativecommons.org/licenses/by-nc/2.0/)*