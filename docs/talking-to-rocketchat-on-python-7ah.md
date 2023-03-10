# 与火箭交谈。与 Python 聊天

> 原文：<https://dev.to/jadolg/talking-to-rocketchat-on-python-7ah>

我遇到[火箭。聊天](https://rocket.chat/)大约一年前，在我看来这是我用过的最好的面向团队的聊天软件之一。它有一个干净漂亮的界面，支持 Markdown 和表情符号。分享代码和让你在频道上发布重要信息是很棒的。它是 Slack 或 Mattermost 等软件的开放替代软件，你可以在自己的基础设施上完美地运行它。我爱上了我可以轻松部署它，并开始玩它，并在 [demo.rocket.chat](https://demo.rocket.chat) 附近逗留，在那里我得到了很大的支持。因为我是一名开发人员，而且我对自动化很感兴趣，所以我想制作机器人来让我的朋友们的生活更轻松，于是我开始挖掘寻找一个好的火箭。Python 的聊天 API 包装器或类似 Telegram 的机器人 API，但失败了。
事实证明，没有制作机器人的机制，但使用内部 Hubot 和 API 包装是过时的，缺乏覆盖范围或开发起来很复杂，所以我决定开始编写自己的程序，并用它来编写机器人。
所以这就是结果:[https://github.com/jadolg/rocketchat_API](https://github.com/jadolg/rocketchat_API)。没什么特别的，相信我。只是花了一些时间去阅读关于 REST API 的精彩的[文档](https://docs.rocket.chat/developer-guides/rest-api),并尽我所能地把它打包，这样我就不用每 5 分钟回顾一次文档了。
想试一试吗？
让我们从 pypi `pip3 install rocketchat_API`开始安装，在 [demo](https://rocket.chat/) 实例上创建一个帐户后，让我们尝试连接并在 **#general** 房间上写一条消息。

```
from rocketchat_API.rocketchat import RocketChat

rocket = RocketChat('user', 'pass', server_url='https://demo.rocket.chat')
rocket.chat_post_message('good news everyone!', channel='GENERAL') 
```

Enter fullscreen mode Exit fullscreen mode

很棒吧？因为它是建立在[请求](http://docs.python-requests.org)库之上的，所以对 API 的每个调用都将返回一个请求响应对象，并且包含代理支持。
你想看频道列表吗？就这么简单:

```
from pprint import pprint

pprint(rocket.channels_list().json()) 
```

Enter fullscreen mode Exit fullscreen mode

每个方法都有与官方火箭相同的文档字符串。聊天 API 文档，它被称为类似的，所以你可以实际上使用官方文件，而与库透明地工作。所有人都在这里。我希望它对你有用，如果你有兴趣投稿，请给我发拉请求，问题，明星:-)