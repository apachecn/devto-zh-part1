# 提升 Shell 脚本

> 原文：<https://dev.to/caramelomartins/on-elevating-shell-scripts-15ai>

自动获得超级用户权限似乎总是很神奇。我对它的工作原理有一个模糊的概念，我非常清楚它们潜在的有趣用途。

我总是看到那些神奇的安装程序在一个(不那么)漂亮的 GUI 后面施展他们的黑魔法，我想知道“他们怎么能把东西安装在我的用户似乎够不到的地方？”。

事实证明，这并不难，而且可以在相对透明的情况下实现。

最近，我的任务是写一些安装脚本，这些脚本必须访问`/usr/local/`。正如大多数人所知，这通常是普通用户的禁区。大多数只会读书，甚至什么都不会读。

所以我开始考虑如何在没有 GUI 帮助的情况下，将文件安装在用户无法访问的地方。

我对事件的版本是通常的版本:把它们变成剧本。这个解决方案会产生类似于
的结果

```
sudo script.sh 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这很讨厌，因为你没有帮助你的用户。他们需要事先知道，他们需要以超级用户的身份运行该命令，并以超级用户的身份实际运行该命令。我放弃了这个想法，因为它简单但不是最佳的。

我继续在谷歌上寻找一个更好的时机来做这件事。我马上找到了几个答案，从“这太糟糕了”到“你可以这样做”。

我找到的最好的资源之一是 [unix.stackexchange](http://unix.stackexchange.com/) 上的[这个](http://unix.stackexchange.com/questions/28791/prompt-for-sudo-password-and-programmatically-elevate-privilege-in-bash-script)答案。

通读这个答案，我对五件事很感兴趣，在那些片段中:`$EUID`、`$0`、`$@`、`$?`和`$UID`。这些看起来像有趣、有用的 Bash 环境变量。我做了更多的研究来找出它们是什么。

我再次把注意力转向 StackOverflow，具体来说就是[这个答案](http://stackoverflow.com/questions/27669950/difference-between-euid-and-uid)、[这个](http://stackoverflow.com/questions/27669950/difference-between-euid-and-uid)和维基百科。我并不总是使用 StackOverflow，但有时它非常有用。`$EUID`和`$UID`，代表两个独立的事物。`$UID`给出用户标识符，该标识符在系统中标识调用过程的真实用户 ID。我们可以用`$EUID`来给出系统中的有效用户。显然，还有文件系统，保存和真实(`ruid`)。[1]所有这些类型的用户之间的区别对我来说仍然不清楚，但是，据我所知，超级用户总是将`$EUID`设为 0。一个重要的注意事项是，这只对`bash`有效。

其余部分也是`bash`变量:

*   `$0`是一个位置参数，表示正在执行的脚本的文件名。所以这实际上是论点 1。

*   `$@`代表参数，“无解释或扩展”。它将输出写入的参数。

*   将显示命令的退出状态。

所有这些都可以在[高级 Bash-Scripting Guide 的第 9 章](http://tldp.org/LDP/abs/html/internalvariables.html)中进一步研究。

投票最高的答案是一个有趣的方式来问系统:“你是以超级用户的身份运行这个吗？”。如果系统回复说它不是以超级用户的身份运行，它将重新运行脚本，连同它的参数，预打印`sudo`。完整回答很简短但是很有力:

```
if [$EUID != 0]; then
    sudo "$0" "$@"
    exit $?
fi 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这个解决方案的任何地方，您都要让用户知道需要提升权限。这提供了一个有趣的命令行界面，提升您的权限级别，而用户不必知道他必须以超级用户的身份运行它。

当然，所有这些都有必须考虑安全问题。话又说回来，作为一个超级用户，运行任何东西，而不信任你正在运行的东西，也不是一个好的选择。

[1][http://man7.org/linux/man-pages/man2/getuid.2.html](http://man7.org/linux/man-pages/man2/getuid.2.html)

*最初发表于 2017 年 3 月 27 日 [hugomartins.io](https://hugomartins.io/) 。*