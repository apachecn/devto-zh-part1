# TIL:带 VSCode 的 psql

> 原文：<https://dev.to/bradymholt/til-psql-with-vscode-1l1a>

昨天我正在赶着吃早餐，偶然看到克雷格·克斯坦的《隐藏的宝石》。列表中有一些非常好的东西！引起我注意的一件事是这个:

> @ l _ avrot–我们可以在 psql 中使用 vim 编辑器的事实

真的吗？我不知道。告诉我更多。所以我用谷歌搜索了一下，当然 Craig 的另一个帖子标题是[我如何使用 Postgres-Psql，我的 PostgreSQL 管理员](http://www.craigkerstiens.com/2013/02/13/How-I-Work-With-Postgres/)出现了。在这里，他解释了如何在 psql 中使用`\e`“允许您在自己选择的编辑器中查看和编辑上次运行的查询”。这听起来非常有用，尤其是在处理更长、更复杂的查询时。

我的“首选编辑器”是 [VSCode](https://code.visualstudio.com/) ，当我试图使用`\e`并将我的`EDITOR` var 设置为“Code”时，它不起作用。当前查询未传递给 VSCode，从 VSCode 保存文件时，没有任何内容发送回 psql。

更多的搜索和摆弄之后，我发现我需要在启动 VSCode 时添加`-w`参数，这样它就会“在返回之前等待文件被关闭”，通过`--help`输出。此外，我了解到您可以使用`PSQL_EDITOR`来设置特定于 psql 的编辑器，我更喜欢这样做，因为我喜欢将`EDITOR`设置为 vim 来处理各种 shell 事务。编辑 SQL，但是，我更喜欢用 VSCode 而不是 vim，非常感谢。所以，现在我在我的。zshrc:

```
export PSQL_EDITOR="code -w" 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我从 psql 输入`\e`时，VSCode 启动并显示我当前或上一次执行的查询的内容，保存文件会导致 psql 执行查询。厉害！