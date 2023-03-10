# 围棋中的松弛陌生人机器人

> 原文：<https://dev.to/plutov/slack-stranger-bot-in-go-3op>

[![wizeline-random](img/1984260146b6ed2c7b9c49fcfb5640d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kvuXPG6C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pliutau.com/wizeline-random.png)

我喜欢在短时间内编写程序，就像用黑客马拉松的方式。在越南，我们不常举行黑客马拉松，所以昨天我决定为自己做一个，时间限制在 3 小时内。我们的目标是构建/部署一些有用的东西，人们可以尝试一下。我决定用 Bot 换 Slack(或者以后再换一个信使)。

所以我写了这个陌生人机器人，在你空闲的时候认识陌生人，探索新的人。它是匿名的和私有的，Bot 不存储任何数据。

### 它是如何工作的？

*   用户打开与陌生人机器人的私人对话。
*   类型`Hi`。
*   陌生人机器人寻找随机的活跃用户，目前不参与陌生人对话。
*   Bot 会将用户发送给 Bot 的所有后续消息转发给陌生用户。没有提到是谁发的这条信息。
*   任何用户都可以键入`Bye`结束对话，并再次键入`Hi`开始一个新的随机对话。

下面是 Wizeline 的一个例子:

> alex.pliutau 【晚上 10 点 29 分】
> 
> 你好
> 
> 随机巫师应用程序 [晚上 10 点 29 分]
> 
> 连接到一个随机的陌生人...
> 
> 发现陌生人！打个招呼，请礼貌一点。键入“再见”结束对话
> 
> 你好。！
> 
> alex.pliutau 【晚上 10 点 31 分】
> 
> 很好，超级私密
> 
> 因此，随机找一个人聊天是可行的
> 
> 随机巫师应用程序 [晚上 10 点 31 分]
> 
> 泰国或高棉的佛教寺或僧院
> 
> 哈哈哈
> 
> alex.pliutau 【晚上 10 点 31 分】
> 
> 再见
> 
> 随机巫师应用程序 [晚上 10 点 31 分]
> 
> 再见！你和陌生人的谈话结束了。如果您想随机开始一个新的，请再次键入 hi。
> 
> 随机巫师应用程序 [晚上 10 点 31 分]
> 
> 再见！你和陌生人的谈话结束了。如果您想随机开始一个新的，请再次键入“hi”。

### 试试看

*   克隆人[松弛陌生人机器人](https://github.com/wizeline/slack-stranger-bot)
*   在空闲时间创建应用程序并复制`API Token`
*   安装[对接器](https://docs.docker.com/engine/installation/)
*   使用有效令牌运行`docker build -t stranger . && docker run stranger -e SLACK_TOKEN=<token>`

### 投稿

所有代码都是开源的。欢迎[贡献](https://github.com/wizeline/slack-stranger-bot/pulls)或提出[问题](https://github.com/wizeline/slack-stranger-bot/issues)。

[我博客中的原帖](http://pliutau.com/slack-stranger-bot-in-go/)