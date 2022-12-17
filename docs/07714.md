# 内容联合的网络标准

> 原文：<https://dev.to/scalawilliam/web-standards-for-content-syndication>

[https://www . Scala willian . com/we B- standards-for-content-syndication/](https://www.scalawilliam.com/web-standards-for-content-syndication/)

我从 2004 年开始制作网站，让我印象深刻的一件事是通过 HTTP 同步内容是多么的脆弱，以及如何即时完成。例如，当我写一篇博文时，我希望它能同步到我自己的网站上，也许会发布到 Twitter 上，或者得到一个 trackback/pingback——许多不同的目标有许多不同的意图。

也许这不是一篇博客文章，而是一个自动生成的项目。它可能是紧急的或不紧急的，可能是频繁的或不频繁的。

在本文中，我只对单向数据共享感兴趣，所以 WebSockets 不在讨论范围内。

## 拉

通过轮询拉取意味着每隔 X 分钟向服务器发起一个请求，然后检查内容是否发生了变化。

这引入了最大延迟，并需要设置计划任务。此外，您还需要确保不超过目标服务器的使用限制。

Atom 和 T2 RSS 有利于提供结构化的拉取方法。

## 推送

推送意味着你的内容一发布，你就打电话给不同的目标，通知他们发生了什么。它是反应性的。

推动有三种方式。如果你想接收推送，你需要托管一个动态服务器，这是一个缺点，尤其是当你使用静态托管时。

### 预定义的一组目标

如果你想在发布时自动发推，你可以调用 Twitter [silo API](https://indieweb.org/silos) ，但是现在你必须为每个目标定制它。这通常发生在 HTTP POST 中。

### 自主管理用户

我有一个有趣的用例是在 [ActionFPS](https://actionfps.com/) 中，我希望在玩“部落战争”时自动将锦标赛数据输入 Challonge。我的一种方法是直接调用 Challonge(当前的，但是脆弱的)，另一种方法是创建一个单独的服务(类似于微服务)来对这些事件做出反应。

但我希望其他人也能做出反应。所以我打算试用一下 [WebSub](https://www.w3.org/TR/websub/) ，这是 2017 年 4 月 11 日的 W3 候选人推荐，之前被称为 [PubSubHubbub](https://en.wikipedia.org/wiki/PubSubHubbub) 。

这是我们在微服务中应该做的；非紧密耦合的方法。

### 基于内容的目标

...比如提提。有一个新的标准叫做 web reference，它类似于传统的 xml-rpc、pingback 和 trackback。

网络提及促成对话。这是规范:[https://www.w3.org/TR/webmention/](https://www.w3.org/TR/webmention/)——这是 W3C 在 2017 年 1 月 12 日提出的建议。

实际上，如果你想接收 web references，你可以在 HTML 中放置一个`<link rel="webmention"/>`,当有人提到你的页面时，他们会调用这个端点。

因为这种方法，你可以[使用像](https://webmention.net/implementations/) [Bridgy](https://brid.gy/) ( [开源](https://github.com/snarfed/bridgy))这样的免费服务自动将你的内容发布到 Twitter 和其他地方，还可以联合回复**回**！你不需要集中化或者上面的“让我们写我们自己的 Twitter 适配器”的方法。不错！

## 推拉

这是我最喜欢的方法:EventSource(又名服务器发送事件)。W3 草案在这里:[https://www.w3.org/TR/2011/WD-eventsource-20111020/](https://www.w3.org/TR/2011/WD-eventsource-20111020/)-它也是反应式的。

除了 IE 之外，event source[在大多数浏览器中都可用——但是你可以为 IE](http://caniuse.com/#feat=eventsource) 使用一个[聚合填充。](https://github.com/Yaffle/EventSource)

EventSource 有一个很好的 [Node.js 库](https://github.com/EventSource/eventsource)，并且还附带了可靠性——如果你错过了一个事件，那么你可以从你看到的最后一个事件中恢复。几周前，我对那个库做了一点小小的贡献，这样我就可以[将 ActionFPS](https://github.com/actionfps/actionfps-clone-logs) 上的日志实时同步到我的本地机器上。还有一个针对 SSE 的 [Scala 客户端。](http://developer.lightbend.com/docs/alpakka/current/sse.html)

优点是你的接收者是客户端而不是服务器，缺点是它是一个持续的连接。

## 推送 API

专为网络浏览器设计。[推送 API: W3C 工作草案 2017 年 05 月 08 日](https://www.w3.org/TR/push-api/)。使用[服务人员](https://developers.google.com/web/fundamentals/getting-started/codelabs/push-notifications/#register_a_service_worker)。

# 进一步阅读

感兴趣的可能还有:

*   [https://indieweb.org/micropub](https://indieweb.org/micropub)
*   [https://switchboard.p3k.io/](https://switchboard.p3k.io/)
*   [https://indivib . org/](https://indieweb.org/)
*   [https://indieweb.org/own-your-data](https://indieweb.org/own-your-data)
*   [http://microformats.org/wiki/microformats-2](http://microformats.org/wiki/microformats-2)

# 总结

基本上，完全根据内容交付的频率和紧急程度来决定使用这种方法:

*   不频繁&不紧急，没有服务器接收-拉取
*   不频繁和紧急，有服务器接收-推送
*   紧急、仅浏览器推送 API
*   频繁，没有服务器接收-拉-推