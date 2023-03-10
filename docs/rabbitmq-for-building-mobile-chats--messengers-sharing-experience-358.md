# RabbitMq 用于建立移动聊天和信使。分享经验

> 原文：<https://dev.to/dianamaltseva8/rabbitmq-for-building-mobile-chats--messengers-sharing-experience-358>

到目前为止，移动信使开发已经非常流行。今天，有数以百万计的移动聊天和信使，其中最著名的是 Viber，WhatsApp 或 Telegram，它们各有利弊。

为了具有竞争力，这种移动应用必须能够支持繁重的负载，并且能够轻松扩展到数百万(有时数十亿)用户，这给开发者带来了一定的挑战。这就是为什么我们建议在这些解决方案中集成现成可靠的消息平台。

这种集成将使得能够将可扩展性问题的解决方案转移到消息平台侧，并且允许将更多的注意力放在新功能开发上。

非常适合 messenger 开发的平台之一是 [RabbitMQ，这是一个开源的多协议消息传递代理，](https://www.rabbitmq.com/) ，它支持各种消息传递协议，如 AMQP、MQTT、STOMP 等。

RabbitMQ 有许多 [客户端](https://github.com/rabbitmq/rabbitmq-dotnet-client) 库，允许它与几乎任何客户端应用平台集成。

**RabbitMQ 包含以下组件:**

*   生产者——创建消息的客户；

*   消费者——接收信息的客户。

*   队列–存储消息的无限队列；

*   exchange–允许将发送给它的消息路由到不同队列的组件。

通常，RabbitMq 中组件的交互如下:producer 向 exchange 发送消息，然后 exchange 接收消息并将其路由到订阅它的队列。

根据交换的类型，可以根据将队列连接到交换的关键字和消息中的关键字的对应关系来过滤消息，或者将消息路由到所有感兴趣的队列，之后消费者从他/她订阅的队列中接收消息。

**交易类型:**

*   扇出–将发送给它的消息路由到所有连接的队列

*   direct 使用 routeld(可以是一个单词)根据过滤设置将发送给它的消息路由到连接的队列

*   主题–根据过滤设置，使用由几个单词组成的 routeld 将发送给它的消息路由到连接的队列，从而实现更灵活的过滤。

**一般来说，系统包括以下部件:**

*   RabbitMQ
*   后端应用程序；
*   客户端应用:安卓、iOS。

要使该体系结构发挥作用，需要在后端应用程序首次启动时就已经建立了交换和队列。

**主要交易所和队列:**

*“conversation . outgoing”*是一个交换，类型为 fanout，在我们的例子中，它需要接收来自客户端的传入消息，并将它们路由到队列。

*“conversation . incoming”*是一个交换，类型 topic，用于向某些用户的交换发送已经处理的消息。在我们的例子中，主题类型允许只向用户发送那些与他们参与的对话相关的消息。

*“聊天-应用-消息”*是后端应用处理传入消息的队列。

初始化所有使用的交换和队列后，我们将交换“conversation.outgoing”与队列“chat-application-messages”连接，并从队列“chat-application-messages”创建后端消息处理器。

系统所有部分的集成从用户注册功能开始。

当用户使用其中一个客户端在服务器上注册时，后端应用程序使用唯一生成的名称在 RabbitMQ 中进行扇出类型的交换，并将该交换名称返回给用户注册的客户端应用程序。

从那时起，所有授权的客户也将获得这个已经存在的交换的名称。

在收到这个用户的交换名后，客户端在 RabbitMQ 中创建一个临时队列，这个队列只在客户端与 RabbitMQ 消息代理连接期间存在，并将其与这个交换连接。

为每个用户创建一个唯一的交换允许同时接收来自所有客户端(iOS、Android)的消息。

在客户端关闭互联网后，或在任何其他情况下，当与 RabbitMQ message broker 的连接丢失时，RabbitMQ 将自动删除服务器上的临时队列，从而防止冗余消息传递并优化我们的交付。对于新的连接，将创建新的队列。

注册之后，用户就可以发送和接收消息了。

发送消息发生在对话中。系统必须能够创建与一个或多个联系人的对话。

创建会话时，每个用户的交换链接到后端应用程序“conversation.incoming”的交换，使用会话 id 作为 routeId。

查看更多关于使用 RabbitMq 创建移动应用程序的 **[。](https://smartym.pro/blog/using-rabbitmq-message-broker-for-messenger-development-some-clues-for-developers/)T6】**

另外，深入了解 [RabbitMq 教程和安装指南。【T2](https://www.rabbitmq.com/getstarted.html)

**关于移动聊天应用中用户数据安全和隐私的一点信息**

关于移动聊天应用的开发，出现了关于用户数据隐私和通信安全的安全问题。

目前，端到端加密(E2EE)是保护用户数据并消除黑客、政府和安全服务查看任何数据(包括聊天消息)的可能性的最可靠方式。

大多数流行的手机信使已经注意到提供安全。例如， [WhatsApp 在 2016 年整合了 E2EE 加密，从而获得了竞争优势](https://techcrunch.com/2016/04/05/whatsapp-completes-end-to-end-encryption-rollout/) 。

了解如何使用 end2end 加密来保护移动应用程序并确保数据隐私。【T2