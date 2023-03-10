# NATS 流持久日志的使用案例

> 原文：<https://dev.to/byronruth/use-cases-for-persistent-logs-with-nats-streaming>

## 什么是持久日志？

在这个上下文中，*日志*是一个有序的消息序列，您可以添加到其中，但是不能返回并更改现有的消息。*持久*位仅仅意味着它们会被记住，并且在服务器重启后仍可能持久(在磁盘上)。

## 什么是 NATS 流？

[NATS 流媒体](http://nats.io/documentation/streaming/nats-streaming-intro/)是一个轻量级的流媒体平台，构建在 [NATS](http://nats.io) 之上，为持久日志提供 API。

它的一些功能包括:

*   轻量级，用 Go 编写
*   单一二进制，零运行时依赖性
*   有序的、基于日志的持久性
*   至少一次交付模式
*   自动用户偏移跟踪
*   支持在流中重放消息

这些属性类似于 [Apache Kafka](https://kafka.apache.org/) 所提供的有序的、基于日志的持久性流。这两个系统之间肯定有差异，但我们不会在这里讨论它们。在我看来，NATS 流(和 NATS)最好的特点是操作它和客户端 API 的简单性。想了解更多，就留下评论:)

## 用例

我将假设对[发布-订阅模式](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)有基本的了解，这是 NATS 流提供的核心 API。但是即使没有，你也不应该有困难。

下面是可以使用发布-订阅模式的特定变体解决的用例列表。我将展示如何使用 NATS 流提供的`Subscription` API 解决这些问题，并讨论提供的语义和保证。

从订户的角度来看...

*   “我只想从流中接收消息”
*   “我想从中断的地方开始，以防断线”
*   “我是新来的，我想阅读这条河流的全部历史”
*   “我要*正好有一次*处理”
*   “我想分担处理邮件的工作”

## 设置 NATS 流

如果你想编码并尝试这些模式，[从 GitHub](https://github.com/nats-io/nats-streaming-server) 下载一个 NATS 流媒体版本。还有一个[官方码头工人形象叫做`nats-streaming`](https://hub.docker.com/_/nats-streaming/) 。

假设您下载了单个二进制文件，您可以解包它并使用以下选项运行它:

```
$ nats-streaming-server \
  --store file \
  --dir ./data \
  --max_msgs 0 \
  --max_bytes 0 
```

默认情况下，NATS 流使用内存存储。`--store`选项用于将其更改为基于文件的文件，该文件可以在重启后继续存在。将`--max_msgs`和`--max_bytes`设置为零，以保留所有通道的所有消息。否则，服务器将默认为 100 万条消息或大约 100 MB 的大小，在这种情况下，将删除通道中的消息，使其低于达到的任何限制(从而删除历史)。

一旦它在 shell 中运行，我们就可以开始写代码了。对于代码示例，我将使用 [Go 客户端](https://github.com/nats-io/go-nats-streaming)。在[下载页面](http://nats.io/download/)上有几个官方客户端和几个社区建设的客户端。

## 样板代码

首先我们需要建立联系。

```
package  main  import  (  "log"  stan  "github.com/nats-io/go-nats-streaming"  )  // Convenience function to log the error on a deferred close.  func  logCloser(c  io.Closer)  {  if  err  :=  c.Close();  err  !=  nil  {  log.Printf("close error: %s",  err)  }  }  func  main()  {  // Specify the cluster (of one node) and some client id for this connection.  conn,  err  :=  stan.Connect("test-cluster",  "test-client")  if  err  !=  nil  {  log.Print(err)  return  }  defer  logCloser(conn)  // Now the patterns..  } 
```

### “我只想接收流中的消息”

这个用例是通过使用具有默认配置的订阅来解决的。

```
handle  :=  func(msg  *stan.Msg)  {  // Print message data as a string to stdout.  fmt.Printf("%s",  string(msg.Data))  }  sub,  err  :=  conn.Subscribe(  "stream-name",  handle,  )  if  err  !=  nil  {  log.Print(err)  return  }  defer  logCloser(sub) 
```

真的就这么简单。NATS 流保证消息按顺序接收和处理。一个警告(将在后面的示例中解决)是，如果在确认(ACK)消息已经被处理到服务器时出现问题(例如断开连接或超时)，那么消息将在稍后重新传递(在处理了前面的消息之后)。

同样，如果在处理过程中出现错误，默认情况下*不会*发送 ACK。这可以通过添加订阅选项来解决:`stan.SetManualAckMode()`

```
handle  :=  func(msg  *stan.Msg)  {  // If the msg is handled successfully, you can manually  // send an ACK to the server. More importantly, if processing  // fails, you can choose *not* send an ACK and you will receive  // the message again later.  // This will only fail if the connection with the server  // has gone awry.  if  err  :=  msg.Ack();  err  !=  nil  {  log.Prinf("failed to ACK msg: %d",  msg.Sequence)  }  }  conn.Subscribe(  "stream-name",  handle,  stan.SetManualAckMode(),  ) 
```

您可能会想，如果第一次失败了，为什么要重新传递消息呢？这取决于什么样的失败，但如果是暂时的失败，那么第二次或第三次可能会奏效。如果这是你代码中的一个错误，并且它永远不会成功怎么办？

### “我想从中断的地方继续，以防断线”

使用默认选项时，订阅仅在*在线*时被跟踪。也就是说，如果客户端稍后重新订阅，它将只接收新消息。它不会接收任何离线时发布的消息。

对于某些用例，可能需要“从您离开的地方开始”，例如工作队列、[数据复制流](https://en.wikipedia.org/wiki/Replication_(computing))和 [CQRS 架构](https://martinfowler.com/bliki/CQRS.html)。

使订户“可恢复”就像添加另一个订阅选项一样简单。

```
handle  :=  func(msg  *stan.Msg)  {  // ...  }  conn.Subscribe(  "stream-name",  handle,  stan.DurableName("i-will-remember"),  ) 
```

`stan.DurableName`选项接受您为该特定订阅提供的名称。它被绑定到流，所以您可以为不同的流重用相同的持久名称，并且每个流的偏移量将被单独跟踪。

在前一节的最后，我问了如果您的处理程序代码有一个 bug 会发生什么。有了持久订阅，您现在可以自由地让订阅者离线，修复错误，然后从中断的地方恢复在线。

要知道处理程序是否失败，您应该记录这些错误，但是当第一个*不可重试的*错误发生时，您也可以立即断开连接。

```
// Declare above so the handler can reference it.  var  sub  stan.Subscription  handle  :=  func(msg  *stan.Msg)  {  err  :=  process(msg)  // Close subscription on error.  if  err  !=  nil  {  logCloser(sub)  }  }  sub,  _  =  conn.Subscribe(  "stream-name",  handle,  stan.DurableName("i-will-remember"),  ) 
```

由于消息是按顺序处理的，因此在出现第一个错误时关闭订阅将会阻止后续消息的处理。重新连接时，失败的消息将被重新传递，然后是所有新消息。

这种方法也保证了完全有序的处理*,不管什么*。失败之后的消息将不被处理，因此重新传递不能与新消息交错。

这种有保证的排序也可以通过使用`MaxInFlight`选项和手动确认来实现。

```
handle  :=  func(msg  *stan.Msg)  {  err  :=  process(msg)  if  err  ==  nil  {  msg.Ack()  }  }  conn.Subscribe(  "stream-name",  handle,  stan.DurableName("i-will-remember"),  stan.MaxInflight(1),  stan.SetManualAckMode(),  ) 
```

即使不关闭订阅，这也保证了消息将按顺序被处理和重试，因为一次只有一条消息是“正在传输的”。过去的例子没有这种限制，因此会有多个消息排队等待处理。

### “我要*整整一次*处理”

上面两个例子中的一个小疏忽是，如果处理成功了，但是 ACK 失败了，会发生什么？NATS 流媒体有一个“至少一次”的传递模型，这意味着如果服务器没有得到确认，它将继续重新传递相同的消息。

为了满足这种情况，客户端必须承担一些保留它成功处理的最后一条消息的责任。

```
var  lastProcessed  uint64  handle  :=  func(msg  *stan.Msg)  {  // Only process messages greater than the last one processed.  // If it has been seen, skip to acknowledge it to the server.  if  msg.Sequence  >  lastProcessed  {  if  err  :=  process(msg);  err  !=  nil  {  // Log error and/or close subscription.  return  }  // Processing successful, set the `lastProcessed` value.  atomic.SwapUnint64(&lastProcessed,  msg.Sequence)  }  // ACK with the server.  msg.Ack()  }  conn.Subscribe(  "stream-name",  handle,  stan.DurableName("i-will-remember"),  stan.MaxInflight(1),  stan.SetManualAckMode(),  ) 
```

服务器维护由客户端*确认*的最后一个消息 ID，但是为了确保只处理一次，客户端也必须维护它的世界视图。为了在重启后实现这一点，客户端需要持久化某人的`lastProcessed`值，并在启动时加载它。但是这可以像包含最后处理的消息的 ID 的本地文件一样简单。

### “我想看看这条小溪的全部历史”

这个用例最适用于想要基于流构建一些内部状态的消费者。事实上，[这种方法正是许多数据库为了支持查询而维护内部索引的方式。所有更改首先被写入日志(为了持久性)，然后一个内部进程将这些更改应用到内存中的索引以支持快速查找。](https://en.wikipedia.org/wiki/Write-ahead_logging)

除非您正在构建一次性索引，否则通常您希望使用持久订阅，因此在重新启动时，只需要处理一小部分更改。从*开始，起点*只是另一种选择。

```
handle  :=  func(msg  *stan.Msg)  {  // ...  }  conn.Subscribe(  "stream-name",  handle,  stan.DurableName("i-will-remember"),  stan.DeliverAllAvailable(),  ) 
```

当您想要部署需要处理旧消息的内部状态的新版本时，这是一个很好的模式(因为您发现了一个 bug 或者正在应用更多的特性，等等)。).这可以离线完成，需要多长时间都可以。构建完成后，它可以与旧版本一起部署，流量可以路由到新版本。

### “我要分担处理消息的工作”

到目前为止，每个用例只需要一个订户来完成工作，因为在这些用例中*排序*被认为是重要的(也许第一个用例除外)。但是，如果排序不重要，或者消息处理可以并行进行(以后可能会进行协调)，那么您可以利用“队列订阅者”。

队列订阅者允许多个客户端使用相同的“队列名”订阅同一个流，消息将被分发到队列组的每个成员。

```
handle  :=  func(msg  *stan.Msg)  {  // ...  }  conn.QueueSubscribe(  "stream-name",  "queue-name",  handle,  // options: durable, manual ack, etc.  ) 
```

上述所有选项仍然适用，包括*耐久性*。只需添加一个`DurableName`选项，您就有了一个持久的队列订阅。

## 后记

### 总是用`SetManualAckMode()`

这提供了对打包的控制，即使它给句柄函数增加了几行额外的代码。如果没有别的，可以记录一个确认失败，这是[当前没有使用隐式确认](https://github.com/nats-io/go-nats-streaming/blob/master/stan.go#L467-L473)完成的。

### 从消息开始

在假设所有东西都需要持久之前，考虑要处理的消息类型是很重要的。具体来说，它们对时间敏感吗？如果是这样，那么订户不需要持久，或者如果是持久的，那么句柄函数需要知道这一点并跳过过期的消息。

同样，考虑一下是否真的需要全排序。基本上，如果流中的任何消息依赖于前一个消息的处理结果，那么就需要全排序。这时应该使用`MaxInflight(1)`,或者在出现错误时应该自动关闭订阅。

### 【恰好一次】同`QueueSubscribe`

“恰好一次”的例子只适用于单个订户。对于一个“队列订阅”,`lastProcessed` ID 需要能够被队列订阅的所有成员集中(并且原子地)访问。如果需要的话，最简单的方法是使用共享的键值存储，它支持设置值的原子操作。

### 示例模拟

我收集了一些例子来突出上面讨论的一些场景。提供了可运行的示例以及自述文件中的输出来说明这种行为。