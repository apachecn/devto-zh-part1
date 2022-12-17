# 网络太慢了

> 原文：<https://dev.to/ben/the-web-is-too-damn-slow>

过去一周，我参加了为期三天的 O ' Reilly Velocity Conference(T1 ),该会议主要讨论 web 性能和 devops。我计划本周就会议涉及的关键话题认真写几篇文章，但这篇文章不在其中。这只是一个咆哮。

在最近的某个时候，我感觉自己走出了柏拉图的慢速网络洞穴，并意识到我正在建设的网站可以快得多。从那以后，我对我制作的每样东西的性能都倾注了很多心血。性能一直是我关注的一部分，但对于网络，我现在比以往任何时候都更觉得它就是一切。人们不会等着你的慢速网站。已经很难撼动表演瘾了，觉得有必要在话题上福音化。

自从离开这个众所周知的洞穴，除了在我自己的 web 开发中关注性能之外，我还关注人们对他们网站的速度和渲染性能的期望，以及他们如何描述“好”的性能。我发现人们用秒来说话，尤其是在讨论糟糕的网络状况时。这对我来说太疯狂了。我们的低门槛让人们只能等待一个空白的白色屏幕，而交付的内容可能会在几毫秒内通过网络发送，正确的工程重点是消除延迟瓶颈。很多都是关于第三方脚本，比如广告和分割测试。作为一名开发人员，也许有些决定超出了你的控制范围，也许你正忙着疯狂地从你的洪水船上舀水，而没有关心你的网站的真正潜在性能，但是如果你有机会传播关于性能的好消息，请这样做。

这个名为 [dev.to](../../) 的网站远非完美，但至少我对它的渲染性能倾注了很多心血。在过去的[中，我已经写了一些关于这方面的内容，将来我会写得更多，但现在我只想对类似的“内容”网站做一个并排的比较。将来我可能会做一个更科学的基准，但是现在我只是展示一些网站的幻灯片供你阅读。它们都是在禁用缓存的相同条件下拍摄的，每次都是在网站内的文章页面上模拟新的访问。这是一个非常有偏见的比较，部分是为了美化我的网站，但内容网站的生死取决于它们呈现的速度，有些羞愧是理所当然的。如果你在手机上阅读这篇文章，图片可能会很小，但你应该仍然能够得到要点。](../ben/faster-rendering-on-the-web)

说到生存和死亡，如果网络不能把它的屎拉在一起，Facebook 即时文章和其他强大的中央机构会吃掉更多的屎，并吸收这么做的利润。网络本身可能是“即时”的，但就其目前的状态来看，却不是。我在这里展示的网站没有一个是特别糟糕的违规者，但是网站，我们今天通常构建它们的方式，是非常慢的，它们不需要如此。

## 美国有线新闻网；卷积神经网络

[![CNN.com filmstrip diagram](img/9c6dd4cf39c05932e9555ef769f96c41.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nm36Crnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/SYgcy9D.png)

## MICROPHONE 的简称

[![](img/1da95254d3abf3abda24cba9f78b90fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--THnH7pVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/NEJQiLn.png)

## ESPN

[![](img/ef88a0a57f17f5cc73ab3f7ac12282f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lwJ-YFQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/JOPi0XV.png)

## 美国 Yahoo 公司(提供互联网的信息检索服务)

[![](img/d6c195d58206778f407ae41b5e7e77ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PyAgsgNt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/t9sdO9h.png)

## 福克斯新闻频道

[![](img/e98eca92760703689d67153dcd52cb9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CnJfDZvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/gCWGgkl.png)

## BuzzFeed

[![](img/c4e32557cc0dc7f1c25275cf4a0b1e51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E5wtijvN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/OtUy1PO.png)

## TechCrunch

[![](img/8dd4da7eda2806220a8f4ec2ee1ad818.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ixYAmJTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/CpmQ1ra.png)

## 中等

[![](img/620b944e5c7191b5a9f2396cd857508b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6jklhMwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/dst5Ydr.png)

## Dev.to

[![](img/5ead1cb775c720458270869e939a397a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b7WbkOXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/UKSgvIQ.png)

这是一个令人瞠目的最好例子。页面可以在 13 毫秒内被阅读的事实令人难以置信，但这并不是每次都发生。这是另一个结果:

[![](img/2a7b2f4b1c9528452f153281d12f433d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7w5stirU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/TapAOSx.png)

这个网站的性能是以毫秒来衡量的，人们的期望是，在恶劣的网络条件下，事情应该保持良好。如果你优先考虑，这些结果是可以实现的。

## 最后

[![](img/5fdc1dc674669f9524bad3d2610cf74f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--97OqQRWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://digital8.com.au/sites/default/files/you_should_feel_bad_zoidberg_meme_imgflip.png)

网络很慢。它并不意味着缓慢，也不一定要缓慢，但它确实如此。那些让 web 变慢的东西，比如第三方脚本，并不是唯一的广告方式，不是唯一的分割测试方式，也不是唯一的跟踪方式。如果我们开始关心用户体验，并把性能作为其中的一个关键部分，我们，网络将会变得更好，更不用说许多网络在许多发展中国家由于膨胀而基本上无法使用，但这是它自己的抱怨。

有很多关于 HTTP/2、服务工作者和其他新的创新和标准的讨论。这些都很好，但不会成为银弹。让网络更快的唯一方法是把它作为一个优先事项，并提高我们对网络速度的预期。