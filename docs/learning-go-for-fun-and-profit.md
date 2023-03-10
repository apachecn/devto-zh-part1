# 学习是为了乐趣和利益

> 原文：<https://dev.to/dvdmuckle/learning-go-for-fun-and-profit>

好吧，也许除了知识之外没有实际利润。但是很有趣！

了解 C 的好处之一是能够学习许多其他语言，而不必偏离“C 公式”没一会儿就出了一个合适的 C 的“续作”。Go 或 golang 是由以 UNIX 和 C 闻名的 Ken Thompson 和 Google 的一些人创建的。基本上就是带包管理器的 C 2.0。这真是太棒了！它不仅是一种简单的语言，使用和获取库也是轻而易举的事情。一旦开始，就可以非常容易地开始构建其他 Go 库，或者创建自己的东西供他人使用。

那么，我是怎么学会围棋的呢？嗯，凭借对 C 语言的了解，我已经知道了最基本的东西。这能阻止我搜索一切吗？没有。例如，如果你使用另一个库中的函数，比如说来自`fmt`的函数，你必须大写这个函数，所以你要做`fmt.Sprintf`。这与 C 语言有一点不同，在 C 语言中，大部分情况下是不区分大小写的。还有多行字符串、其他一些奇怪的格式，以及在变量创建方面的巧妙技巧。我最喜欢 Go 的一点，也是你最先学到的，是短变量赋值和隐式类型化。你不必再指定 1 是一个整数！就说`x := 1`。当然，有些情况下，你可能想指定`x`是一个值为 1 的浮点数，或者说`y`有一个字符串值`true`。

我的大部分知识都是在我朋友的项目 Irkbot 中完成的。Irkbot 是一个使用 go-ircevent 库用 Go 编写的模块化 IRC bot。因为它是模块化的，所以编写新功能非常容易。我做的第一个特性是 [@interject](https://github.com/dvdmuckle/irkbot/blob/master/lib/module/interject.go) 命令，它简单地打印出流行的 Linux/GNU copypasta 和命令后面的内容，否则默认为 Linux。我花了…比应该做的更长的时间来做这么简单的事情，但是我最终明白了。这也给了我一些为别人编码的经验。我做的大部分工作要么是为了自己，要么是为了成绩，所以我很少根据反馈修改代码。这是一个有趣的过程，看到其他人如何格式化他们的代码很酷。

我做的下一个功能是 [@xkcd](https://github.com/dvdmuckle/irkbot/blob/master/lib/module/xkcd.go) ，这是一个基于搜索词搜索相关 xkcd 漫画的命令。为此，我使用 https://relevantxkcd.appspot.com 的[作为后端，并使用 go-xkcd 库来匹配漫画编号和漫画名称。这需要做更多的工作，因为我以前从未处理过这样的 HTTP 请求。](https://relevantxkcd.appspot.com)

我最近做的一个功能是 [@doom](https://github.com/dvdmuckle/irkbot/blob/master/lib/module/doom.go) ，它利用非常有趣的[宁静 doom](https://github.com/jeff-1amstudios/restful-doom) 来玩 Doom！这是一大堆有趣的事情，当它开始工作的时候，它是一个爆炸。我还调整了 Irkbot 的配置，使其能够连接到需要/PASS auth 的 IRC 服务器，因此它可以连接到 Twitch chat。虽然这意味着人们可以在 Twitch 聊天上玩毁灭战士，但这种滞后使得实际上几乎不可能完成任何事情。我很想在某个时候和更多的人一起测试这个，所以谁知道呢。

到目前为止，我真的很喜欢围棋，我还没有发现它有任何问题。我计划在未来扩展 Irkbot 的功能，甚至创建一些全新的东西，与 ELK 堆栈结合使用。敬请关注！或者关注一下我的新实验室。

*帖子[学围棋为乐为利](https://dvdmuckle.xyz/index.php/2017/08/20/learning-go-fun-profit/)最早出现在 [dvdmuckle](https://dvdmuckle.xyz) 上。*