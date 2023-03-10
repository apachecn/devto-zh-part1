# RabbitMQ 在线索生成系统开发中的应用

> 原文：<https://dev.to/dianamaltseva8/using-rabbitmq-in-lead-generation-system-development>

在这篇文章中，你会发现一个在软件开发中应用 RabbitMQ 的实际例子。

[RabbitMQ 服务器](https://www.rabbitmq.com/) 是一种队列服务器，允许不同的应用程序按照 AMQP 协议进行交互，被 Google、微软、NASA、Red Hat、VMware、AT&等公司广泛使用

[RabbitMQ](https://www.rabbitmq.com/getstarted.html) 用于在以下选择的服务处理资源密集型和休眠任务时开发服务架构:

xero–网上记账服务
安全交易–支付处理服务

RabbitMQ 用于建立服务器之间的通信；也许，这是实现这一目的的最有效的解决方案之一。

想象一下，该公司有以下问题:随着客户数量的增加，大量时间花费在诸如发票创建、付款请求、付款验证和确认(这些操作是手动进行的)等金融服务操作上。

**任务:**

通过第三方 Xero API 自动创建发票，并通过电子邮件交付发票；通过安全交易 API 从用户卡自动扣款。

【cron 解决方案

第一个煎饼失败了。我们创建了一个脚本，该脚本每周运行两次，并在单个流程中执行必要的操作。这对整体性能造成了限制(来自 API 的长时间应答等待)。此外，其中一个阶段的任何错误都会导致整个流程的工作结束，并且需要重新启动整个脚本。

因此，我们得出的结论是，我们需要将代码分成几个独立的进程，并在它们之间进行通信。

了解更多有关使用 [RabbitMQ 构建销售线索生成系统](https://smartum.pro/blog/using-rabbitmq-in-building-the-system-of-leads-generation/) 和[创建信使](https://smartym.pro/blog/using-rabbitmq-message-broker-for-messenger-development-some-clues-for-developers/)的信息。

另外，请随意分享您使用这项技术的经验！)