# 用 Go 构建 Twitch.tv 聊天机器人-第 1 部分

> 原文：<https://dev.to/foresthoffman/building-a-twitchtv-chat-bot-with-go---part-1-i3k>

编辑(2019 年 5 月 13 日):自撰写本文以来，该软件包已从“twitchbot”重命名为“bot”。但是，用于本教程的标记版本仍然使用旧名称。

对于那些不熟悉的人来说， [Twitch.tv](https://twitch.tv) 是一个直播平台，提供所有与游戏相关的创意。不一定只是电子游戏。内容创作者玩棋盘游戏、滚动游戏(例如龙与地下城&)，以及介于两者之间的任何游戏。在这个平台上，流媒体工具能够与他们频道的实时聊天室进行互动(有延迟)。

为了促进流媒体和聊天互动，Twitch 使用了一种变体的 IRC，并在他们的 T2 Twitch 开发者页面上提供了实现文档。这使得开发人员可以创建聊天机器人，可以调节聊天室，与聊天者互动，并自动完成流媒体的某些任务。有几个众所周知的，具有许多功能，但在这个演练中建立的聊天机器人不会那么复杂。

## 重要提示

这个项目的代码可以在 [GitHub](https://github.com/foresthoffman/bot) 上找到，在[麻省理工学院许可下](https://github.com/foresthoffman/bot/blob/master/LICENSE)。任何为 Twitch 开发聊天机器人的人都应该阅读并遵守他们的[开发者协议](https://www.twitch.tv/p/legal/developer-agreement/)和他们的[服务条款](https://www.twitch.tv/p/legal/terms-of-service/)。你还应该注意到 [IRC 消息限制](https://dev.twitch.tv/docs/irc#irc-command-and-message-limits)，如果违反了这些限制，可能会导致超时(临时禁止)甚至长时间的 IP 禁止。因此，你应该**遵循规则**，为你的机器人创建一个备用账户(以防出错)。

关于这一点，**我建议总共有两个账户**，一个用于测试机器人，一个作为机器人。这样你就可以在你自己的聊天中测试机器人，而不是在别人的聊天中(这通常不是个好主意)。

## 入门

在本演练中，我们将一步一步地完成聊天机器人在实时聊天室中运行所需的所有功能。

以下是该项目的要求:

*   [已安装 Go](https://golang.org/doc/install)
*   拥有两个 Twitch.tv 账户(一个给你，一个给你的机器人)

一旦你的需求排序，我们就可以开始了！

继续在您的终端中运行以下命令，如果您还没有这样做的话:

```
$ go get github.com/foresthoffman/bot 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以导航到包的源:

Linux/MacOS

```
$ cd $GOPATH/src/github.com/foresthoffman/bot 
```

Enter fullscreen mode Exit fullscreen mode

窗户

```
> cd %GOPATH%/src/github.com/foresthoffman/bot 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以通过运行下面的 Git 命令返回时间:

```
$ git checkout -b walkthrough step-0 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个名为“walkthrough”的全新分支，并将其带到步骤 0，在这里我们将开始编写代码。

如果遇到瓶颈，某个东西无法编译，您可以使用
向前或向后跳一步(其中“#”表示您要跳转到的步骤的索引)

```
$ git reset --hard step-# 
```

Enter fullscreen mode Exit fullscreen mode

想跳到最后，随时可以跑:

```
$ git reset --hard master 
```

Enter fullscreen mode Exit fullscreen mode

## 第 0 步

好的，那么在你最喜欢的编辑器中打开`twitchbot.go`，让我们看看我们在做什么。

```
package twitchbot

// TODO:
// 1\. Connect to a Twitch.tv Chat channel.
//  a. Pass along necessary information for the connection.
//   i.   The IRC (chat) server.
//   ii.  The port on the server.
//   iii. The channel we want the bot to join.
//   iv.  The bot's name.
//   v.   A secure key to allow the bot to connect indirectly (not through the website).
//   vi.  A maximum speed at which the bot can respond.
// 2\. Listen for messages in the chat.
// 3\. Do things based on what is happening in the chat. 
```

Enter fullscreen mode Exit fullscreen mode

因此，忽略许可这一点，除了一些不错的东西之外，我们已经有了一个让机器人运行所需的布局。让我们把重点放在最重要的一点上，也就是第一点。

1.  连接到 Twitch.tv 聊天频道。

为了完成#1，我们需要一些数据:

*   IRC(聊天)服务器
*   服务器上的端口。
*   我们希望机器人加入的通道。
*   机器人的名字。
*   允许 bot 间接连接(不通过网站)的安全密钥。
*   机器人可以响应的最大速度。

使用占位符用户名和 OAuth 代码(我们将使用您自己的代码)，我们有以下数据:

*   irc.chat.twitch.tv
*   Six thousand six hundred and sixty-seven
*   抽搐测试
*   抽搐机器人
*   非统组织:秘密秘密秘密秘密秘密秘密
*   每 20/30 毫秒一次

因此，我们需要机器人来跟踪 5 个字符串和一个时间间隔。因此，让我们在`twitchbot.go` :
中创建一个结构来处理这些数据

```
package twitchbot

import (
    "time"
)

type BasicBot struct {
    Channel string
    MsgRate time.Duration
    Name    string
    Port    string
    OAuth   string
    Server  string
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些名字很好的字段来保存我们的数据。这里有一些事情要记住:`Channel`必须是小写字母，否则机器人将成功连接到服务器，但会对聊天中的任何消息视而不见；通过有一个`OAuth`字段，我们必须硬编码我们的机器人的密码，这不被认为是最佳实践。

让我们接受一个受限访问目录的路径，而不是有一个`OAuth`字段，在运行时可以从该目录读取 bot 的 OAuth 令牌。这样，它可以很容易地被改变，而不必重新编译我们的源代码或重启机器人。

```
package twitchbot

import (
    "time"
)

type BasicBot struct {
    Channel     string
    MsgRate     time.Duration
    Name        string
    Port        string
    PrivatePath string // replaced the OAuth field
    Server      string
} 
```

Enter fullscreen mode Exit fullscreen mode

为了节省空间，我省略了字段注释和 todo-list，但这就是步骤 0。你现在就可以编译你的代码了！

```
$ go fmt ./... && go build 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读！如果你有任何问题，你可以通过推特( [@forestjhoffman](https://twitter.com/forestjhoffman) )或电子邮件(【forestjhoffman@gmail.com】T2)联系我。

## 图像归属

*   卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com/s/photos/twitch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片
*   Glitch，Twitch.tv 版权