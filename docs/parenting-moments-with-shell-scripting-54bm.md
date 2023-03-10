# shell 脚本的育儿时刻

> 原文：<https://dev.to/pauljacobson/parenting-moments-with-shell-scripting-54bm>

我对 shell 脚本(你知道，所有那些命令行的东西)既着迷又有点着迷。原来我的儿子对你从命令行能做什么很感兴趣，它打开了一些非常酷的育儿时刻的大门。

今天下午，我给儿子看了我写的一个小脚本，它打开了 [Firefox 开发者版](https://www.mozilla.org/en-US/firefox/developer/)、 [VS 代码](https://code.visualstudio.com/)、[还记得牛奶吗](https://www.rememberthemilk.com/)，并通过从我的命令行运行一个别名脚本来显示我所在城市的天气预报。天气服务是 Jessie Frazelle 写的一个非常酷的命令行脚本。

> ![](img/24c398348287b3ac1d3bdde5b6cd7227.png)保罗·雅各布森@保罗·雅各布森![](img/4d9c44713c216584b3d48ff3455cbb68.png)这款[#命令行](https://twitter.com/hashtag/commandline)[#天气](https://twitter.com/hashtag/weather)app/服务来自 [@jessfraz](https://twitter.com/jessfraz) 无疑是我今天见过的最酷的东西。🤓[github.com/jessfraz/weath…](https://t.co/UatUl51dJY)2017 年 12 月 04 日下午 13:15[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=937671751273676800)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=937671751273676800)18[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=937671751273676800)104

当我的儿子在 [iTerm2](https://www.iterm2.com/) 中输入一个命令后看到所有这些应用程序都打开时，他大吃一惊。他立刻让我为他创作一些东西。

所以我为他的 Ubuntu PC 写了一个类似的脚本。它的工作方式与我在 Mac 上的不太一样(Linux 有一种有趣的方式来打开一些应用程序，将它们绑定到终端会话),但他现在有一个小命令，可以用来打开 VS 代码，并从他的终端给他提供天气更新。

我喜欢他也喜欢这些东西。他让我给他介绍一些他可以用来学习所有这些 shell 命令的东西。他想把它们写在一张小抄上。我有一些资源([Linux 文档项目](http://www.tldp.org/index.html)是我最近才接触到的一个很棒的项目)我会和他分享，我认为他会从学习这些东西中得到乐趣。