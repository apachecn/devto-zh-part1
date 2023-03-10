# git 指南 git:提交及其消息

> 原文：<https://dev.to/rkoutnik/the-gits-guide-to-git-commits--their-messages-37hm>

git 的核心单元是提交。提交表面上很简单(一些代码更改和一条消息)。这种简单掩盖了内在的复杂性。

## 何时提交

我听到了很多关于何时应该提交的观点，从“尽早提交，经常提交”到“每次发布提交一次”(哎呀)。我发现的唯一能持续减少开发人员头疼的方法是每次变更提交一次。这需要一些规则来将小的变更分解成它们自己的提交，并压缩完成同一变更的多个提交。“改变”本身是主观的，我的经验法则是“对代码最小的编辑，可以认为是完整的”。半个重构可能能够运行并通过测试，但它不是“完整的”。

每次变更提交一次会增加类似 [`git bisect`](///articles/The-gits-guide-to-git-Bisect.html) 和 [`git revert`](https://git-scm.com/docs/git-revert) 的工具。虽然在一次提交中集成几个小的更改很诱人，但是如果其中一个更改需要在以后恢复，那么多次提交会让您更开心。

## 提交消息

从技术上讲，提交消息并不令人兴奋。有两种写法。如果您运行`git commit`，git 将帮助您打开一个编辑器来编写提交消息。(不幸的是，编辑器总是 Vim，git 被认为是开发人员知道如何退出 Vim [0]的唯一原因)。另一方面，您可以添加`-m`标志来添加内联提交消息:`git commit -m 'my commit'`。

另一方面，如果你不是神话中的 10 倍程序员，你会和其他人在一个团队中工作。(如果是的话，[我们可以用一个像你这样的人](///2016/04/29/Wanted-Ninja-Rockstar-Code-Monkey-Hacker-Unicorn.html))。即使你*单飞，你也在一个由现在的你、一个月前的你、两个月前的你等组成的团队中。我们都需要一些线索来追踪二月美国在想什么疯狂的逻辑线索。*

这就是为什么像这样的提交消息真的让我恼火:

```
commit 32f4410abb2a901016c4a8b23c4b487e36be9916
Author: Randall Koutnik <souper_leet_h4x0r@rkoutnik.com>
Date: Tue Jun 14 15:05:51 2016 -0700

    Fix error on alert page 
```

Enter fullscreen mode Exit fullscreen mode

这家伙在想什么？说真的，他在想什么？我需要知道这次提交的动机——为什么，在他时间的所有可能用途中，这个编码者决定做这个？

### 语境为王

大学的时候考 Calc 2，差一分就过了。我不擅长高等数学。当我在为微积分期末考试临时抱佛脚，在教科书中寻找任何解释的线索来传授对反导数的理解时，我遇到了有史以来最可怕的句子:

> 对这种现象的解释是如此琐碎，以至于留给读者做练习

我已经三天没运动了，所以我去跑步了。这对我理解零件集成一点帮助都没有。这本教科书的作者在微积分方面博览群书，因为他确实写了这本书。他对微积分的了解比我多得多，并认为我能够找到更好的地方。

上面提交消息的作者知道修复了什么 bug。他们的问题是假设读者有相同的背景。

### 三个问题

提交消息需要回答三个问题:

*   什么变了？
*   变革是如何实施的？
*   为什么这种改变是必要的？

上面的提交只回答了“什么”(而且很糟糕)。我们仍然不知道是哪个错误？任何存在了 90 秒的代码都有多个错误。如果我知道在克隆名称中带有&符号的警报时，一个修复程序引入了一个回归，并且我正在梳理历史以试图找到所述提交，那么这个消息几乎是无用的。

此消息也没有提供高级别的“如何”修复。答案是更好的解析吗？限制较少的正则表达式？跳过所有相关的单元测试？在提交消息中回答“如何”可以节省读者阅读 diff 的工作量。

最后，也是最重要的一点，*为什么是*？修正错误当然很重要。为什么会出现这个错误？大多数现代 git 系统允许链接到问题跟踪器；大量应用链接。有些，比如 GitHub，允许你用包含`Fixes #1234`的提交消息来结束一个问题。一石二鸟！

### 更好的承诺

让我们尝试修复上面的提交。使用我们新的消息质量试探法，我们需要添加:

*   *为什么*提交发生了(通过一些外部链接)
*   *变革如何进行*
*   摘要中的更多详细信息

```
commit 32f4410abb2a901016c4a8b23c4b487e36be9916
Author: Randall Koutnik <souper_leet_h4x0r@rkoutnik.com>
Date: Tue Jun 14 15:05:51 2016 -0700

    Fix name parse bug when cloning alert with a '&' in the name

    Previously, the API would choke because we weren't escaping '&' in alert names on cloning.
    Now the clone service escapes the name before the API call.

    Fixes #1337 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了一个简洁的总结，一个对实际修复的解释和一个对外部细节的连接。现在，如果(令人震惊地)系统的某个部分依赖于不可避免的数据，当回归票蜂拥而至时，我们将确切地知道从什么开始提交…

[0]说真的，你可以通过`git config --global core.editor "your-editor-here"`或者环境变量`GIT_EDITOR`来设置 git 使用的编辑器。