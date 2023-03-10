# 开发商 vs 中国防火长城

> 原文：<https://dev.to/samwsoftware/developer-vs-the-great-firewall-of-china-707>

远程工作可能是美好的。无论是远程工作还是从事个人项目，在旅途中作为一名开发人员工作都是令人惊奇的。不幸的是，中国的防火长城让事情变得更加棘手。

## 中国的长城防火墙

中国防火长城是中国政府实施的互联网审查系统的别称。它限制访问政府认为不合适或冒犯性的网站、应用程序、社交媒体和电子邮件提供商。中国在 1994 年第一次有了互联网，到 1997 年，他们颁布了法规，赋予他们审查互联网上几乎所有内容的权力。互联网审查的整个前提是基于邓的一句话:

> “如果你打开窗户呼吸新鲜空气，你会发现一些苍蝇会飞进来”——邓·肖鹏

This can be seen with the blocking of a lot of websites from western countries; Facebook, Google, Twitter and even Medium are all blocked. The Chinese communist party leader, Xi Jinping, wants to keep Chinese culture pure from western influence. Whist this is true, there are a lot of western websites that go through the firewall without trouble. The Chinese government also block websites that store user information. One way to get around this block is to give the government access to that data. WhatsApp has recently been added to the block list. It is thought to be because WhatsApp wouldn’t provide a backdoor or the encryption keys. This was enough for it to infringe on the regulations.

## 为什么它是个讨厌鬼

没有谷歌！直到我来到中国，我才意识到我已经变得如此依赖谷歌。想要快速搜索库中函数的使用，搜索错误消息的修复，或者在 MDN 上找到正确的页面？没有 VPN 你就不会(马上就要谈到这一点)。我已经遇到的其他令开发者沮丧的限制包括无法使用任何常用的 oAuth 方法(谷歌、脸书或 Twitter)，无法访问 Firebase、Heroku 网站(尽管 CLI 似乎可以用)、Dropbox 和 Slack。
目前 Bing.com 确实在中国工作，我开始意识到为什么谷歌主宰了世界其他地方。必应是个垃圾。我预计，中国政府将对 Bing 设置限制，允许其进入中国，这将加剧这种情况。我试着把必应作为我的默认搜索引擎，但一两周之后，我不得不回到谷歌和虚拟专用网上。
什么有效？
尽管许多西方网站受到严格限制，但还是有一些非常有用的网站。最有用的网站是:Github、Udemy 和 Mozilla Developer Network。我肯定还有很多，但这些是我用得最多的。

## VPN——我理智的救星

VPN(虚拟专用网络)是访问你所了解和喜爱的网站的最佳方式，可以绕过防火墙。有很多选择，从免费的应用程序和软件到每月 12 美元的订阅，再到建立自己的应用程序和软件。我要说的是前两个。中国最流行的两种 VPN 服务是 ExpressVPN 和 NordVPN。它们都有漂亮的 Windows 应用程序，并且都在 Linux 下从终端运行(尽管 ExpressVPN 在 Linux 上要容易得多)。到目前为止，我在这次旅行中使用了这两种方法，它们都有各自的优缺点。如果你想每月节省几块钱，并计划在中国长期居住，他们都提供 6 个月或 12 个月的合同，给你一个月订阅的大折扣。我不打算在这里分析它们的性能，有很多网站致力于比较不同的 VPN 提供商。
这两家提供商都有 android 和 iOS 应用，并允许 3 或 6 个并发连接。在 android 上尝试了这两个应用程序后，我最终使用了一个名为 TurboVPN 的免费应用程序。网站上写着“这项服务不能在中国使用”,尽管它仍然对我有效。与 TurboVPN 相比，付费服务的应用程序似乎需要更长的连接时间，而且掉线的频率也更高，我曾试图询问付费服务提供商这是为什么，但从未得到过像样的回应。
在使用所有的应用程序时，我注意到我在无线网络上的连接与在 4G 网络上的连接有很大的不同。我在中国买了一张当地的 sim 卡，发现 4G 网络连接到我的虚拟专用网的效果比 WIFI 好得多。

## 中国试图夺回控制权

10 月中旬是一次共产党会议，为了准备这次会议，防火墙进行了一些重大升级。为了确保没有抗议或骚乱，政府将 WhatsApp 加入了屏蔽名单。这是因为其加密服务过去曾被用于组织抗议活动。除了阻止越来越多的消息服务之外，对 VPN 使用的控制也显著增加。大约有两周时间，我无法使用任何付费的 VPN 服务。幸运的是，我的 TurboVPN 应用程序不知何故仍然工作，但这给我的许多工作设置了巨大的障碍。VPN 服务已经更新了他们的软件，看起来工作更有效了，但是这只是防火墙和 VPN 提供商之间的猫捉老鼠的游戏。我认为政府现在的目标是更大的付费服务和传统的互联网网络。这可以解释为什么 TurboVPN 仍然可以工作，而其他所有的都不行，以及为什么 4G 比 WIFI 更好。有传言称，中国正在准备立法，规定互联网服务提供商需要屏蔽任何 VPN 流量，这将极大地改变中国的互联网接入问题。

## 结论

旅行和工作是一个惊人的组合，无论是远程工作，从事个人项目，还是储蓄，然后在世界各地旅行时学习新技能，我都全力推荐。尽管如果你计划访问中国，在你来之前，先准备好一个(或几个)虚拟专用网。在中国建立一个虚拟专用网是非常困难的，没有虚拟专用网的生活是棘手的，没有虚拟专用网的工作几乎是不可能的。中国政府正在加强对互联网的控制，并堵住传统上被利用的漏洞。也许用不了多久他们就会完全控制，除非你是顶级黑客。
如果你正在考虑搬迁一个月以上，也许可以考虑中国以外的其他地方。香港是一个不错的选择，因为它没有防火墙，你仍然可以很容易地去中国最好的地方旅游。

如果你喜欢这篇文章，请把它传播出去，并关注我以获得更多关于工作和旅行的帖子。

*最初发布于 Medium.com*