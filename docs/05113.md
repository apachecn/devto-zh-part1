# 使用 Atom 中的 Regex 删除所有 console.log()

> 原文：<https://dev.to/michael/remove-all-consolelogs-using-regex-in-atom>

在 JavaScript 文件中使用`console.log()`可以很好地调试代码。但是当涉及到将你的代码发布到产品或 git repo 时，最好通过删除`console.log()`来清理你的代码。

如果您使用 Atom 作为文本编辑器，使用 Regex 很容易做到这一点。

1.  首先通过前往**查找>在缓冲区中查找**，拉起在缓冲区中查找栏。
2.  在标题为*在当前缓冲区中查找*的第一个搜索字段中，键入 **console.log.*$**
3.  选择图标**指定的搜索面板右上角的*使用正则表达式*选项。***
4.  按下**查找所有**查找所有`console.log()`的实例，然后按下**替换所有**

[!["Find in Buffer panel"](img/0e098598fe05487508dd50e228e89fc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cE6MEdAd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/krce0r4mjcgeoaivqf4d.png)

在步骤 2 中，我们使用一个正则表达式来获取字符串`console.log`的所有实例，直到行尾。通过使用`.*$`,`.`表示我们想要匹配任何字符，`*`用于表示我们想要匹配任意数量的字符，最后`$`用于表示匹配到行尾。

如果你想知道为什么我们在缓冲区面板中留下了*查找的第二个字段，那是因为我们不想用任何东西替换所有的`console.log()`。如果留空，就是说不用任何东西替换。*

* * *

最初发布于[李名炀](https://michaelsoolee.com/remove-console-atom-regexp/)