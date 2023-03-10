# 公司如何利用你对技术的无知和对黑客的恐惧

> 原文：<https://dev.to/antoinette0x53/how-companies-prey-on-your-ignorance-of-tech-and-your-fear-of-hackers>

## “其他都没有安全感”

认识一下 Nomx。
[![nomx](img/fd157dafb0fe4032ca5b416249e99398.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hBIl0lkz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9czv5koftk9zw46aptw9.png) 
“正在申请专利的 nomx 协议通过与平台无关的协议提供安全、加密的电子邮件、消息、音频和视频通信服务。”这种创新的协议通过一种物理设备交付给您，“允许用户使用传统的电子邮件或消息客户端来传输和接收安全的通信。”
**Nomx:其他都没有安全感**
[![shutupandtakemymoney](img/4584648f12b85f73639bdde23401a4d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S6jssX9n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0uni0g72ogntapsir1ry.jpg)

您会购买该产品吗？考虑考虑...我会等的。

如果我告诉你 Nomx 盒子里是一个树莓派呢？你还印象深刻吗？好的，然后呢...如果我告诉你 Nomx 的特殊协议是运行在 Raspbian 上的过时版本的 Postfix 和 Dovecot 呢？

你现在开始明白我要去哪里了吗？如果你猜对了“Nomx 全是 sh**”，那你就猜对了。英国安全研究员 Scott Helme 被 BBC 要求检查 Nomx 设备，因为许多人对此非常兴奋。该公司声称他们是最安全的，因为谷歌和雅虎已经被黑客攻击，他们可以保证用户的电子邮件不会被黑客攻击。Scott Helme 发现 Nomx 很大程度上让人感觉索然无味。我不会在这里重复这些，但是如果你感兴趣的话，可以看看他在博客上写的文章。

## “实际保护您的 SSL 非常昂贵，而且需要很长的过程”

接下来，在 ISP 立法后不久，每个人都开始认真考虑使用 VPN 来满足他们所有的浏览需求(网飞除外)。在那段时间里，一家名为 MySafeVPN 的公司突然出现，加入了这场行动。这里有一些问题。第一个问题是，MySafeVPN 自称是另一家名为 Plex 的公司的附属公司。Plex 强烈否认与 MySafeVPN 有任何关系。

[![tweet](img/f545f690457abef15f15e8ea1aad9007.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9owh2YCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6muhs46dqg7nuzpfl435.PNG)

疯狂？更疯狂的是，MySafeVPN 的计费网站(奇怪的是，它把你带到了 myvpnhub.com)并不安全。VPN 网站上缺少 HTTPS 不会激发信心。当被问及丢失 SSL 证书时，上面的引用是他们的回答。事情从那里开始走下坡路了。原来 Plex 几年前有一次数据泄露，泄露了电子邮件地址，所以这解释了 Plex 的客户都收到了一封电子邮件，称这项新的 VPN 服务与 Plex 有关。整个丑陋的局面演变成了安全研究人员和 MySafeVPN 之间的 twitter 之战、种族诽谤和一个粗略的电话。
[![tweet](img/d76d7c2fb77b98fd4314e165aa8a04c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kf7DyJNL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2spq5rjjyku1rneg5qmo.PNG) 
[你可以在特洛伊·亨特的博客](https://www.troyhunt.com/the-importance-of-trust-and-integrity-in-a-vpn-provider-and-how-mysafevpn-blew-it/)上了解一下。MySafeVPN 的 Twitter 账户现在被暂停了(可能是因为种族诋毁或者撒谎和使用窃取的电子邮件地址来推广他们的业务，这很难说)。

## 没有什么是百分百安全的

像 Nomx 和 MySafeVPN 这样的公司依赖于这样一个事实，即你很可能不知道加密、网络、黑客等是如何做到的。作品。他们把一堆听起来很有意义的专业术语拼凑在一起，祈祷你分辨不出它们的区别。(“我们的计费网站不需要 SSL，因为我们实际上是通过我们自己的 VPN 加密 hyperloop 隧道发送流量回来的”我这样做对吗？).他们利用你对随时可能被黑客攻击的恐惧，同时告诉你，除非你购买他们的产品，否则你将无能为力。

不要被他们的声明所迷惑，你可以做一些事情来避免被骗去购买平庸的安全服务

*   在你购买一件产品之前，先对它进行研究。很可能有人(可能是安全研究人员)已经看过并写过相关的文章。
*   不要相信任何说自己写加密算法的公司。说真的。就是不要。永远不会。
*   警惕任何声称是世界上最安全的公司。事实是，100%的安全是一个神话，任何告诉你不是这样的人都是在玩弄你。

我们在安全领域有这样一句话，“当我们谈论黑客或数据泄露时，这不是‘如果’的问题，而是‘何时’的问题”。它发生在每个人身上，包括公司和个人，只是规模和影响程度不同。在你的个人生活和工作中，你是你最好的防御手段。在采取行动之前，花时间提醒自己存在风险是保护自己的最佳方式。

查看 Matt Kiser 的[普通人的互联网安全指南](https://dev.to/mattkiser/the-normal-persons-guide-to-internet-security)获取提示。