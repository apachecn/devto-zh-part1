# 掌握 IRC 工具 irssi +脚本

> 原文：<https://dev.to/terceranexus6/mastering-irc-tool-irssi--scripting-3jp6>

我必须承认我是 80 年代和 90 年代技术的超级粉丝。我从小就喜欢赛博朋克的东西，十四岁前一直戴着绿色的实验室护目镜，三岁开始使用电脑。我喜欢学习技术领域的最新工具，并保持与时俱进，但我对经典有着天然的吸引力。我钦佩他们是我们现在所拥有的一切的开始，这样**我只是想了解 IRC 频道的乐趣**。

[![](img/4128276547a73a7c8ac94a4c2fef3ad4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xLhKVd3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/ELzh4.jpg)

但是后来我发现了一个在 linux 终端上管理 IRC 的非常有趣的工具，它允许你随时加载 perl 脚本。起初，我认为我可以模仿一些经典的与朋友的交流，但除此之外，我还可以利用脚本的魔力。

首先，我用的是这个:

*   Linux(对我来说是 debian jessie)终端
*   Freenode irc 频道
*   适用于 Linux 的 irssi
*   Perl、python、bash 或其他脚本语言

所以首先是下载工具:

```
sudo apt-get install irssi 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了`irssi`就执行它，只在终端中写入`irssi`。在`[(status)]`命令行中，你可以使用命令`/SET nick yournickname`选择一个不同于终端中的昵称，如果你不使用这个，它会自动从终端捕捉你的用户名。也可以这样设置代理:

```
/SET use_proxy ON
/SET proxy_address <Proxy host address>
/SET proxy_port <Proxy port> 
```

Enter fullscreen mode Exit fullscreen mode

设置完成后，就该连接了。首先连接到 Freenode:

```
/CONNECT Freenode 
```

Enter fullscreen mode Exit fullscreen mode

一旦你进入(你应该等一会儿)使用`/join thenameofyourchannel`加入你的频道。

这些是快速设置的基础。有关网络的更多信息，只需在`[(status)]`命令行中使用`/help network`。渠道`/help channels`也是如此。使用`/list networks`或`/list channels`来列出这些。还有一些有用的命令:

```
/help [command] - more information about a command
/eval ${whatever} - evaluates the given command, for example S - server N - username
/cat file_dir/file - the same as bash cat command
/exec - executes commands in the background like, ls
/cd - same as linux terminal
/alias name command - give alias to custom commands. useful with online roleplaying
/ping user - ping an user. Also a Channel. 
```

Enter fullscreen mode Exit fullscreen mode

还有更多。除此之外，您可以**设置一个自动 perl 命令，该命令在启动 irssi** 时启动。例如，如果你想要一个私人问候，你应该这样写:

```
#hello.pl
#special greeting

print "Hello there master, welcome again.\n"; 
```

Enter fullscreen mode Exit fullscreen mode

并保存在`~/.irssi/scripts/autorun/`中。如果你想自己执行，保存在`~/.irssi/scripts`中，在`[(status)]`命令行中使用`/script load hello.pl`即可。而且，你可以使用`/exec perl ~/.irssi/scripts/hello.pl`在通道中执行它们，不仅仅是 perl，任何东西都可以。我试着用 python 命令启动了一个 Flask 应用程序。现在，一旦你发现了这一点，你就可以用 perl 创建更好的脚本，或者使用 [this repo](https://scripts.irssi.org/) 中的脚本。

但是！更有趣。默认情况下，irssi 将一个**日志文件**保存在`~/irclogs/$tag/$0.log`中，但是您可以使用`/SET autolog_path ~/new_path/irclogs/$tag/$0.log`来更改它。然后我想，嘿，这些东西对我有什么用呢？嗯，看你的需要了。例如，您可以创建一个脚本来检查日志并查找某些单词。例如，假设您有一个 raspberry pi 并安装了 irssi。你得到的脚本在后台工作，并打开 irc 频道。你离开 rpi，然后走开，接下来你连接到 irc 的任何地方，写下*咒语*。

像这样，你可以去玩所有的选项。当然，我们不要忘记**这里所有的在线角色扮演**选项...

总之，irssi 易于使用，灵活有趣。推荐你试试！