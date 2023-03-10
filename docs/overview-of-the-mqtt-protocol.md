# MQTT 协议概述

> 原文：<https://dev.to/kenwalger/overview-of-the-mqtt-protocol>

有几种不同的方法可以用来将物联网(IoT)设备连接到网络。我曾经在中讨论过 [NodeMCU 开发板](https://www.amazon.com/gp/product/B01N0B48NI/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01N0B48NI&linkCode=as2&tag=kenwalgersite-20&linkId=0046d38f856ec3e7daa13be68ab68b3b) [![](img/b6e425cf78b32ba03fff0fa67ae66022.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B01N0B48NI) 连接 WiFi 网络的一些设置。在物联网环境中，HTTP 等成熟的网络协议可能有点繁重。一个流行的、轻量级的解决方案，我想讨论一下 ismqtt 的基础知识。

### MQTT

虽然 mqtt*曾用*代表 MQ 遥测传输，但它现在被归类为**，而不是作为首字母缩写的**。那是什么呢？它是做什么的？嗯，它是一个使用 TCP/IP 框架的 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 提供发布/订阅服务的消息协议。它过去和现在都是为小型受限设备设计的，并根据这些限制做出设计决策。物联网世界中的重要概念，如内存、带宽、延迟、功耗和网络可靠性。让我们关注 MQTT 的一个主要概念，发布/订阅模式。

### MQTT 发布/订阅模式

在发布/订阅模式中，一个客户端发布信息，另一个客户端可以*订阅*它想要的信息。在许多情况下，在客户之间有一个中间人来促进和/或过滤信息。这允许实体之间的松散耦合。

解耦可以以几种不同的方式发生，空间、时间和同步。

*   空间-订阅者不需要知道谁是发布者，例如通过 IP 地址，反之亦然
*   时间——两个客户端不必同时运行
*   同步——发布和接收不会中断操作

通过在代理上完成的过滤，并非所有订户都必须获得相同的消息。代理可以根据主题、内容或消息类型进行过滤。因此，客户端可以只订阅关于温和数据的消息。或者仅包含关于离心机内容的消息。或者，也许我们只想接收关于特定类型错误的信息。

想象一个物联网场景，例如，一个 NodeMCU 设备连接了一些环境传感器，如 [TMP36 温度传感器](https://www.amazon.com/gp/product/B00JYQAIBM/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00JYQAIBM&linkCode=as2&tag=kenwalgersite-20&linkId=8821b4ca99905bdc27b91f31a6cf6025) [![](img/9aed9817baa7f6648aa1f35768d80390.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B00JYQAIBM) 和[湿度传感器](https://www.amazon.com/gp/product/B00AFCNR3U/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00AFCNR3U&linkCode=as2&tag=kenwalgersite-20&linkId=6ec157d39e27d84ac9e6ede2c459c641) [![](img/b1ec9f1c8a742a58cb5d31c178c503dc.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B00AFCNR3U) ，我们可以发布这些数据。与我们的经纪人有联系的一些客户可能对此感兴趣，其他人可能不感兴趣。他们会订阅他们想要的信息。

[![MQTT Publish-Subscribe Model](img/d407828e927627b20078691dc84ca922.png)T2】](https://www.kenwalger.com/blog/wp-content/uploads/2017/05/pub-sub-model.png)

一旦连接到代理，发布客户机只需将其数据发送给代理。一旦到达那里，代理将适当的数据转发给已经订阅了该数据的客户机。同样，可以过滤这些订阅。所有这些数据传输都是以一种为小型、资源有限的设备设计的轻量级方式完成的。

###### 信息包

上图中显示的消息包只是一个例子。除了消息或有效负载之外，真正的数据包还会包含附加信息，如数据包 ID、主题名称、服务质量(QoS)级别。数据包中还包括标志，这样代理就知道消息要保留多长时间，以及消息是否重复。

### 包裹

这只是 MQTT 的冰山一角，还有其他几个有趣的特性。保留的消息、服务质量、遗嘱、持续会话和系统主题等功能。鉴于安全性在当今世界的重要性，MQTT 也具有安全特性也就不足为奇了。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我在物联网主题上发布的最新消息。如果你喜欢这篇文章，或者有问题，请在下面留下评论。或者在我的[博客](https://www.kenwalger.com/blog)上看到原文。