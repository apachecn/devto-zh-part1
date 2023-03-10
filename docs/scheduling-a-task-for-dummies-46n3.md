# 为傻瓜安排任务

> 原文：<https://dev.to/saurabhgoyal/scheduling-a-task-for-dummies-46n3>

# 什么-

`Basic concept is that there is a work at hand and there is some related task to be done, but not right away. Some situations when scheduling a task may be needed instead of doing right away are -`

*   当由于较低的优先级、所需资源的不可用性、任务的复杂性、运行任务的成本、`priority of the main work at hand`或各种其他原因，任务现在不被完成时。然后，您计划要完成任务`later`。

*   当任务被分配给一个或多个其他人去做的时候。你需要一个共同的平台来与他们交流任务。您将该任务安排在他们工作列表中。

*   当任务以一定的频率重复时，例如每天或每周。您安排任务运行`periodically`。

现在我们如何安排？让我们看看涉及的组件。就其核心而言，任务只不过是要在特定数据集上执行的一组指令。这是调度任务时需要传递的数据。组合的数据可以被称为`Message`,它从调度器传递给将执行该任务的任何人。

# 怎么-

## 协议

我们需要一个协议(一套规则或合同)来在机器(阅读计算机)中以有效和可互操作的方式做到这一点。用于此目的的协议称为`Messaging Protocol`。我们在日常生活中使用的最常见的消息传递协议之一是邮局服务，我们必须提供目的地、来源、添加所需的邮票并将内容封装在包装中，以便完成消息传递。在计算机中，SMTP(简单邮件传输协议)就是这样一种协议，它为电子邮件服务提供动力。

`If you are wondering why a separate protocol, let's just think this way - a protocol is nothing but a refined version of whole or parts of one or more other protocols customized for one specific purpose.`

以下是使用特定消息传递协议优于其他协议(如 HTTP/IMAP)的好处

*   HTTP 是一种同步协议，在点对点连接上工作，这意味着每当发送方发送一条消息时，它将只被一个接收方接收，并且处于同步模式，即如果在接收方完全接收到数据之前有任何中断，数据将会丢失。这对于客户端(接收方)将再次请求的 web 应用来说可能是好的，但是对于需要保证消息传递到一个或多个客户端的消息传递来说是不合适的，这些客户端在发送消息时可能可用也可能不可用，

*   HTTP 的发送方和接收方彼此高度依赖，通信的每一部分都高度依赖于对双方的正确理解和知识。这阻止了消费者、发布者和其他部分在任何地方以任何级别的能力相互独立。

*   消息传递协议仅为消息传递而创建，因此它们以多种方式优化连接建立和请求-响应周期，使其成为实现此目的的解决方案。

由于大多数消息传递协议利用子部分(如发布、路由和消费)的异步执行，因此它们被称为异步消息传递协议。有各种这样的协议——AMQP、MQTT 等。

## 组件&流程-

现在您已经有了消息和发送它的协议，还缺少什么呢？`Consistency`和`Reliability`。简单来说，我们需要确保-

*   在人们得到它之前，信息不会以任何方式被破坏/改变。

*   信息到达预定的人。

*   最重要的是，在丢弃消息之前，消息中的任务会成功执行。

对于上述情况，一个好的方法是有一个`message storage`和一个`storage-manager`来防止丢失，并在出错时管理路由和消息持久性。所以有-

*   要传递的消息，包括要执行的任务和执行任务的数据。

*   产生消息的一个或多个发布者

*   一个或多个消费消息的消费者。

*   消息存储队列是最常用的数据结构。

*   经理——连接所有部分并促进信息传递的程序/服务/中间人。

这听起来可能很简单，但是消息可靠性的全部好处、由于通用标准而在各种资源之间的互操作性、消息、发布者和消费者方面的可伸缩性以及发布者和消费者之间的依赖性的消除是异步消息传递协议带来的全部好处。

一般来说，有两种流行的消息传递模式-

*   发布者发布特定主题的消息。消费者订阅某些主题。每当有新消息可用于某个主题时，所有订阅的消费者都会收到该消息。

*   发布者将消息添加到队列中，消费者使用该队列。一条消息只由一个消费者使用。

# 让我们谈谈 AMQP——

AMQP(异步消息队列协议)是一个特定的协议，它使用了上面提到的组件，并添加了一些其他组件。

[![AMQP Model-1](img/fa792d97022fe9e57fcb98e558b09b8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AsBcLpAa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://saurabhgoyal.github.io/asseimg/The-amqp-model-for-wikipedia.svg)*<sub><sup>[https://commons . wikimedia . org/wiki/File:The-amqp-model-for-Wikipedia . SVG](https://commons.wikimedia.org/wiki/File:The-amqp-model-for-wikipedia.svg)</sup></sub>*

商店经理/中间人被称为`Message Broker`。

简而言之，代理维护一个或多个`queues`，所有传入的消息都存储在这里，直到它们按预期被成功消费。这些队列可能在内存中，也可能是持久的。消息可以由代理直接添加到队列中，或者添加另一个称为`exchange`的层，该层根据称为`binding`的规则控制和管理哪些消息要添加到哪个队列中。

消费者被称为`workers`(他们的工作是消费队列)。它们可以根据队列上的负载增加或减少。它们可以被配置为只消耗某些类型的队列，从而将不同类型任务的消耗分配给不同的工作人员。

[![AMQP Model-2](img/711b540ccb891926ccd6f08919f916d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Itb58lw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://saurabhgoyal.github.io/asseimg/exchanges-bidings-routing-keys.png)*<sub><sup>[https://www . cloudamqp . com/blog/2015-05-18-part 1-rabbit MQ-for-初学者-what-is-rabbit MQ . html](https://www.cloudamqp.com/blog/2015-05-18-part1-rabbitmq-for-beginners-what-is-rabbitmq.html)</sup></sub>*

# 通俗名称——

*   `Celery -`只是一个客户端，作为生产者将消息分配给代理或消费来自代理的消息。它基本上是您的应用程序和代理之间的解释器，并使用 AMQP 与代理交互，因此您的应用程序不必做所有这些。其他类似的解决方案有- `pika`、`kafka`等。

*   管理队列并与发布者和消费者交互的代理。具有高度可靠和全面的功能，以确保消息传递和防止任何损失。其他类似的解决方案有- `ActiveMQ`、`Amazon SQS`、`kafka`等。

*   存储键-值对的数据存储，支持某些类型的数据结构作为值，对于高速需求表现非常好。所有数据都存储在内存中，因此通常用作缓存。其他类似的解决方案有- `memcached`、`mongodb`等。**为什么在这里** -它最近通过 AMQP 增加了对出版商-消费者模式的支持，因此作为一个经纪人也越来越受欢迎。

* * *

参考资料:-

*   [数字海洋 AMQP](https://www.digitalocean.com/community/tutorials/an-advanced-message-queuing-protocol-amqp-walkthrough)
*   [RabbitMQ 教程](https://www.rabbitmq.com/tutorials/tutorial-one-python.html)