# AsyncAPI 规范简介

> 原文：<https://dev.to/fmvilas/introducing-the-asyncapi-specification>

*原贴于[blog.hitchhq.com](https://blog.hitchhq.com/introducing-the-asyncapi-specification-7feb57b460ae)T3】*

我们很自豪地在 [Hitch](https://www.hitchhq.com) 、 [AsyncAPI 规范](https://www.asyncapi.org)推出一项开源计划，我们已经为此努力了几个月。一个类似 OpenAPI 的异步 API 规范。

AsyncAPI 规范是与协议无关的，因此您可以将它用于工作在 MQTT、AMQP、WebSockets、STOMP 等之上的 API。基本上就是物联网 API 和异步微服务。

该规范在很大程度上受到了 OpenAPI(又名 Swagger)的启发，并被设计成尽可能保持与它的兼容性。

# 为什么是另一种规格？

在 [Hitch](https://www.hitchhq.com) 我们爱上了 API 机器可读文档格式(OpenAPI，RAML 等。).我们总是鼓励我们的客户为他们的 API 使用机器可读的定义，因为它有很多好处。从更好的文档和代码生成到可扩展的 API 支持。
在内部，我们使用消息驱动的微服务架构，我们无法拥有 HTTP APIs 的所有工具。因为您的 API 是用机器可读的文档格式定义的。此外，与此同时，我们注意到我们的一些客户正在 MQTT 上创建物联网 API，他们无法从 Hitch 中获得所有好处，因为现有的规范不支持消息驱动的 API。

是时候拿出对每个人都足够好的东西了，而不是让每个公司各自为战。如果我们联手，我们可以一起学到很多东西。开始吧！

## 一个基本例子

### 邮件服务

这个例子描述了一个非常基本的电子邮件服务。每当有新用户注册时，它都会发送一封电子邮件。它订阅了`hitch.accounts.1.0.event.user.signup`事件，并在收到后发送电子邮件并发布一条新消息，通知系统电子邮件已经发送。

```
asyncapi: '1.0.0'
info:
  title: 'Sign  up  email  example'
  version: '1.0.0'
baseTopic: 'hitch'
host: 'asyncapi.hitchhq.com'
schemes:
  - 'amqp'
  - 'mqtt'

topics:
  accounts.1.0.event.user.signup:
    subscribe:
      $ref: '#/components/messages/userSignedUp'
  email.1.0.event.email.sent:
    publish:
      $ref: '#/components/messages/emailSent'

components:
  messages:
    emailSent:
      summary: 'Email  sent  to  user.'
      description: 'A  message  notifying  an  email  has  been  sent.'
      payload:
        type: 'object'
        properties:
          user:
            $ref: '#/components/schemas/user'
          content:
            $ref: '#/components/schemas/content'

    userSignedUp:
      summary: 'A  user  has  signed  up.'
      payload:
        type: 'object'
        properties:
          user:
            $ref: '#/components/schemas/user'
          signup:
            $ref: '#/components/schemas/signup'
  schemas:
    content:
      title: 'content'
      description: 'The  email  content'
      type: 'string'
    id:
      title: 'id'
      description: 'Resource  identifier'
      type: 'string'
    username:
      title: 'username'
      description: 'User  handle'
      type: 'string'
    datetime:
      title: 'datetime'
      description: 'Date  and  Time  of  the  message'
      type: 'string'
      format: 'date-time'
    user:
      type: 'object'
      required:
        - 'id'
        - 'username'
      properties:
        id:
          description: 'User  Id'
          $ref: '#/components/schemas/id'
        full_name:
          description: "User's  full  name"
          type: 'string'
        username:
          $ref: '#/components/schemas/username'
    signup:
      type: 'object'
      required:
        - 'method'
        - 'datetime'
      properties:
        method:
          description: 'Signup  method'
          type: 'string'
          enum:
            - 'email'
            - 'facebook'
            - 'twitter'
            - 'github'
            - 'google'
        datetime:
          $ref: '#/components/schemas/datetime' 
```

Enter fullscreen mode Exit fullscreen mode

如果你熟悉 OpenAPI 规范，我相信你已经发现了许多相似之处。但是，代码中的这个`topics`部分是什么？还有这个`accounts.1.0.event.user.signup`是什么？让我们深入研究一下。

## 核心概念

AsyncAPI 规范基于两个核心概念的假设:

### 消息

消费者与您的 API 通信的方式是基于消息的。消息是两个或多个程序交换的一条信息。大多数情况下，通知另一端，或者事件已经发生，或者您希望他们执行某个操作。

从技术上讲，事件和动作总是以相同的方式发送。这些只是消息，其内容可以是任何内容。所以当我们谈论**事件**和**动作**之间的区别时，这只是信息内容的语义差异。我们不强迫你在它们之间做任何区别(尽管我们鼓励你这样做)。

Martin Fowler 在 GOTO
上关于事件驱动架构的演讲一个消息可以包含报头和有效载荷，但是两者都是可选的。为了尽可能保持与协议无关，规范允许您定义任何类型的报头。

### 主题

消息驱动协议通常包含一些可以找到的东西，如主题( [MQTT](http://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices) )、路由关键字(AMQP)、目的地(STOMP)等。在某种程度上，它们可以比作 HTTP APIs 中的 URL。因此，当您向 API 发送消息时，它将根据您发布它的主题进行路由。它允许您创建订阅某些主题和发布到其他主题的 API。

命名主题没有标准方法，所以我们建议您[在这里](https://github.com/asyncapi/topic-definition)看看我们的提议。

## 工装

### 代码生成器

到目前为止，我们只有 Node.js 的代码生成器([github.com/asyncapi/node-codegen](https://github.com/asyncapi/node-codegen))。我们希望你能参与[为你选择的编程语言](https://github.com/asyncapi/asyncapi/issues/new?labels%5B%5D=Code%20Generator%20Request)开发一个。

### 文档生成器和 API 支持

您对为 AsyncAPI 生成文档和/或设置 Hitch API 助手感兴趣吗？我们一定能帮上忙！在 hi@hitchhq.com 给我们写封短信，我们会回复你的。

### 结论

AsyncAPI 规范将让我们改进这类 API 的互操作性和工具。无论是物联网还是内部异步微服务，我们相信这将在异步 API 的未来产生影响。