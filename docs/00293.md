# Webcal 的可悲现状

> 原文：<https://dev.to/kristiandupont/the-sad-state-of-webcal-5hb6>

当涉及到日历数据和事件时，我无法相信网络的现状。

<figure>[![](img/3ab94a6cb6f2d57c87fa9302567ad8ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KdbB0S0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AErpd_TbD76LxPCFU9zVB0g.jpeg) 

<figcaption>照片由[este 扬森斯](https://unsplash.com/photos/zni0zgb3bkQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/calendar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

有一种 iCalendar 格式，它既古老又古怪([非常古怪](https://tools.ietf.org/html/rfc5545#section-3.1))，但它很有效，几乎满足了所有必要的目的。然而，对于一个网站来说，没有一个标准的方法可以将这样的文件添加到你的日历中。是的，在笔记本电脑上你可以下载并双击它。这将允许您的默认日历选择它，但这仍然不是一个好的用户体验。我想让我的用户点击一个链接，并立即有一个对话框说:“你想把它添加到你的日历吗？”。这应该适用于基于浏览器的日历，如谷歌日历，它应该适用于桌面应用程序，最重要的是:它应该适用于手机。

现在，有一个非官方的 webcal://协议方案，它有一些支持，但只对你想*订阅*的日历有效。我不想那么做。我想让我的用户在他们的日历中添加一个单独的非重复事件。我不是唯一的一个，你可以看到脸书和 Eventbrite 提供的令人遗憾的解决方案——在 iOS 上(我不能和 Android 说话)，在你的日历上添加活动是完全禁用的。

这让我感到惊讶，因为如果有一件事推动了网络和技术的进步(除了成人内容)，那就是商业需求。除了电子邮件，我敢打赌日历维护是最常见的业务流程。这是每个人都需要的。

我可以想象出几种简单的解决办法。我们都同意一个互补的 webevent://方案，它不需要订阅。或者，我们向 iCalendar 格式添加一个属性(它甚至可以是一个约定)来指定这一点。然后我们修复各种浏览器中的剩余差距。对我来说，如果我们忽略让浏览器供应商达成一致并实现它们的挑战，这似乎是微不足道的。