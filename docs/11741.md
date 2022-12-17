# 彩色 SSH 终端

> 原文：<https://dev.to/michaelmior/coloured-ssh-terminals-419>

随着将软件部署到生产系统的前景日益逼近，以及在生产系统而不是开发机器上执行意外命令的恐怖故事，我想尝试找到一种方法来确保这种情况永远不会发生在我身上。我的第一个想法是改变终端的背景颜色，因为这将是我所连接的系统的一个清晰的指示器。不幸的是，gnome-terminal 不允许改变正在运行的终端的背景颜色，我也不总是想用 SSH 启动一个新的终端。幸运的是，我找到了解决办法。

在研究改变`gnome-terminal`背景的可能性时，我遇到了 [ROXTerm](http://roxterm.sourceforge.net/) 。ROXTerm 有一个 DBUS 接口，可用于控制开放终端的配置文件和配色方案。不幸的是，关于这个接口的文档有点少。感谢[西蒙](http://news.ycombinator.com/user?id=shimon)对黑客新闻[这篇文章](http://news.ycombinator.com/item?id=2089159)的评论。

我创建了一个名为“警告”的新配色方案，包括红色背景，目的是在任何我知道需要格外小心的终端中使用它。

```
dbus-send --session /net/sf/roxterm/Options net.sf.roxterm.Options.SetColourScheme string:$ROXTERM_ID "string:Warning" 
```

Enter fullscreen mode Exit fullscreen mode

下一步是在我每次连接到这些服务器时执行这个命令。我首先创建了一个简单的脚本来节省一些输入。我把这个放在了`~/bin`(确保把这个加到你的`PATH`)。

```
#!/bin/sh
if [-n $ROXTERM_ID]; then
dbus-send --session /net/sf/roxterm/Options net.sf.roxterm.Options.SetColourScheme string:$ROXTERM_ID "string:$1"
fi 
```

Enter fullscreen mode Exit fullscreen mode

在`~/.ssh/config`中使用本地 ssh 配置文件允许您设置特定于连接的选项。以下配置设置显示了每当生产系统连接到时，如何更改配色方案。

```
Host prod
Hostname production.example.com
LocalCommand roxterm-colour Warning 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以通过 ssh prod 连接，颜色会像预期的那样改变。剩下的问题是一旦连接断开就重新设置配色方案。不幸的是，SSH 没有任何选项允许在连接中断时执行命令。为此，我创建了一个简单的脚本来别名 SSH，并在连接断开后重置配色方案。这个位于`~/bin/ssh`。在这种情况下，你必须确保在你的`PATH`中`~/bin`在`/usr/bin`之前。

```
#!/bin/sh
trap "roxterm-colour Tango" EXIT
/usr/bin/ssh $* 
```

Enter fullscreen mode Exit fullscreen mode

`trap`命令确保每当脚本退出时，配色方案将被重置(我的默认配色方案是 Tango)。最后，我还发现，如果我打开一个新的标签，同时进入一个彩色的终端，这个标签也会呈现同样的颜色。因此，我也在我的中加入了 rox term-color Tango。所以每一个新的终端都会重新设置颜色。

我对这个设置很满意，希望它能帮助我避免犯任何灾难性的错误。我现在已经用 roxterm 替换了 gnome-term 作为我的默认设置。还知道其他避免生产系统出错的技巧吗？请在评论中告诉我！