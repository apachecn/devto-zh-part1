# 从 Netlify 到 Discord 的 Webhook 通知

> 原文：<https://dev.to/netcell/webhook-notification-from-netlify-to-discord-39ol>

从懈怠走向不和谐是一种巨大的快乐。当然，它缺乏 Slack 必须提供的一些功能，但 Discord 客户端超级快速流畅，并配有语音聊天功能。归根结底，性能才是最重要的。

我必须处理的一个问题是将网络通知从松弛状态转移到不协调状态。在这篇文章中，我将谈谈我解决这个问题的过程。然而，如果你像我一样只是在寻找解决方案，那就去 TLDR 吧；。

## 我在想什么

起初，我认为我只需要在 Discord 上创建一个传入的 webhook 来获得一个 webhook url。

之后，在 Netlify 上创建一个出站 webhook。

从 Discord 复制 webhook url 并粘贴到 Netlify 中。

> 你猜怎么着没用！

## 怎么了？

问题是 Netlify 发送的 JSON 格式与 Discord 不兼容。在四处寻找之后，我在 Discord 开发者文档里面找到了这个部分[。](https://discordapp.com/developers/docs/resources/webhook)

## TLDR；

在 Netlify 中，创建一个**Slack integration**webhook(*yeah，Slack* )，粘贴你的 **Discord webhook url** 并追加`/slack`

> 玩得开心:)