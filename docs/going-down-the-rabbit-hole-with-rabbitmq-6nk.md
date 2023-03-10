# 和 RabbitMQ 一起下兔子洞

> 原文：<https://dev.to/bengreenberg/going-down-the-rabbit-hole-with-rabbitmq-6nk>

现代 web 应用程序可能很复杂。许多过程可能需要同时发生。一个流程可能已经启动，但需要另一个流程完成才能结束，而第二个流程依赖于其他也要完成的异步功能。在应用程序开发中，有各种方法来处理这些复杂的层次。当今一些最流行的框架已经内置了帮助解决这些问题的功能。

在过去的一周里，我通过开始学习 [RabbitMQ](https://www.rabbitmq.com/) 来解决处理多个相互依赖的任务的问题。

[![](img/af42955682aacd49782c7cca11c9b57a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---f1bGzPZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKEmYc1dyOpiJiRwo7b-0QQ.jpeg)

什么是 RabbitMQ？RabbitMQ 是一个开源的消息代理软件包。使用 RabbitMQ，您可以建立基础设施来连接应用程序中相互依赖的各个组件。RabbitMQ 通过创建与消息队列交互的生产者和消费者来工作。生产者创建消息，而消费者接收消息。消息存在于消息队列中。消费者总是在监听新消息，当接收到新消息时，它会按照程序中指定的方式进行解释和操作。

让我们用一个简单的“Hello World”消息来模拟这个场景。RabbitMQ 网站包含了大量关于该软件的资料，包括优秀的教程，我强烈建议您深入研究一下。

用 Javascript 编写的“Hello World”消息的生产者将如下所示:

```
#!/usr/bin/env node

const amqp = require('amqplib/callback_api');

serveramqp.connect('amqp://localhost', function(err, conn) {
 conn.createChannel(function(err, ch) {   
   let q = "hello";    
   let msg = process.argv.slice(2).join(' ') || "Hello World!";     
   ch.assertQueue(q, {durable: false});    
   ch.sendToQueue(q, new Buffer(msg), {persistent: true});
   console.log(" Message was sent %s", msg);
 });
 setTimeout(function() {conn.close(); process.exit(0) }, 500)
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们上面所做的是首先设置 [AMQP 客户端库](https://www.npmjs.com/package/amqp-client)，它允许我们发送和接收 RabbitMQ 消息。然后，我们设置一个连接 serveramqp.connect，它有两个参数:我们设置的服务器和一个函数。在这个函数中，我们创建了一个消息通道，它也接受一个函数的参数，其中我们定义了我们的队列(让 q = hello)以及我们在命令行上输入的信息或“Hello World”。我们从那里开始创建队列，并将消息发送到我们的队列。在这样做的过程中，我们还向控制台记录我们将它发送到队列中，因此我们知道它成功了。最后，我们关闭通道连接，并在 5 秒钟后退出进程。

现在，我们需要设置我们的 RabbitMQ 消费者。在下面的例子中，我们将通过为每条消息等待一秒钟来模拟每条消息给我们的程序带来的工作量出现在消息中。您可以运行多个 RabbitMQ 使用者来监听同一个消息队列，每次其中一个使用者接收到一条消息时，它都会处理该消息的内容，但是其他正在运行的使用者可以自由地接收其他消息。只要有可能，系统知道首先发送给开放的消费者，而不是在每个消费者之后创建消息的等待列表。

```
#!/usr/bin/env node

const amqp = require('amqplib/callback_api');

amqp.connect('amqp://localhost', function(err, conn) {
  conn.createChannel(function(err, ch) {
    let q = "hello";
    ch.assertQueue(q, {durable: false});

    console.log("Listening for new messages in %s. To exit press CTRL+C", q);
    ch.consume(q, function(msg) {
      let secs = msg.content.toString().split('.').length - 1; console.log(" [x] Received %s", msg.content.toString());
     setTimeout(function() {
        console.log("Work done");
      }, secs * 1000);
    }, {noAck: true});
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们的消费者职能与生产者职能有一些重叠。就像我们的生产者职能，我们需要要求 AMQP 图书馆。我们还需要定义我们的消息队列，这里很重要的一点是，它的命名要与它在生产者中的命名相同。与我们的生产者不同，消费者不会在一段特定的时间后关闭，因为它的工作只是监听新消息，然后将它们发送到正确的地方进行处理。如上所述，我们通过为每个“.”等待一秒钟来模拟工作负载在消息中:let secs = msg.content.toString()。拆分('.').长度—1；。

我们上面定义的是一个 RabbitMQ 系统的简单模型。RabbitMQ 是一个非常强大的工具，可以集成到一个复杂的应用程序中，我们只是触及了它的皮毛。如果你有兴趣更深入地研究，我强烈推荐你通过他们网站上提供的[教程](https://www.rabbitmq.com/getstarted.html)来学习。上面的代码是他们展示的前两个教程的组合。不过有一个警告:一旦你开始掉进兔子洞，你可能很难再爬上来。