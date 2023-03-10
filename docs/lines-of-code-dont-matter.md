# 代码行并不重要。

> 原文：<https://dev.to/benaryorg/lines-of-code-dont-matter>

我们很久以前就知道 LOC(代码行)是一个可怕的度量单位。嗯，至少我们大多数人都知道这一点。

现在，当我本周五坐下来，用一些内部魔法将一些文本从你的控制台转移到仪表板上时(说起来容易，做起来难，我发现 CouchDB 是首选工具)，最后我怀疑自己的工作效率。

# 结果

最后我得到了两样东西:

*   在我们的时间追踪中，三个小时可能会多一点
*   我们的 GitLab 中有 104 行漂亮的 shell 代码

这甚至还不到每分钟一行代码。
见鬼，它甚至包含了一个用不同的变量名
重复了五次的代码块(要去掉这一部分可不是件容易的事)。

当然，我们确实把整个事情从一个糟糕的`vim file;pandoc file | curl home-grown-nodejs-daemon`移植到了一个更干净的数据库解决方案，并做了一些修改，但是讨论部分只有一个小时左右。

## 脚本

那么这个脚本是做什么的呢？
基本上它

*   使用`getopts`填充一些变量
*   如果 *tty* 是交互的，从 *stdin* 中读取丢失的变量
*   如果缺少强制变量，则失败
*   下载当前文档
*   将当前文档与新条目合并
*   将它推回给 CouchDB，用正确的修订进行包装

看起来很简单，对吧？

# 为什么代码行不好度量。

我已经投入了**很多**的努力让这个脚本尽可能的健壮。
如果你在某个时候输入类似文字的东西`my"name\":{}\x123`，它会以完全相同的方式存储在数据库中。
每个曾经和 shell 脚本打过交道的人都知道，要做到不失败是非常困难的。
这是你逃脱的外壳。
JSON 合并需要对字符串输入进行转义。有可能失败的卷曲。可能出错的地方太多了。

它使用了五行(可能太多)使用变量的紧密封装的 shell，如果没有设置就读取它，但只有在 tty 是交互的情况下才读取，否则失败，转义它(正确地说，不仅仅是简单的`s/"/\"/g`)并将其存储在一个新的只读变量中。
这适用于所有输入，包括在 JSON 中需要特殊转义的特殊字符(想想:“二进制”字符、多字节、地狱甚至表情符号)。

那是五行。在不需要超级特殊转义的编程语言中，你很难将它放入如此紧凑的代码中。

代码行数没有任何意义，因为这是一个可以随意更改的人工数字(空行、移行、注释、。…).

但是，更进一步说，有些任务看起来非常琐碎，但最终却需要大量工作。有时，它们甚至被证明是简单的 T2，但一开始可能并不明显。一个简单的问题往往有一个优雅的解决方案，它是如此优雅和简单，以至于你根本看不到它。