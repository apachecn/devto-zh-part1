# 用 RabbitMQ 延迟重新排队

> 原文：<https://dev.to/realflowcontrol/delayed-requeuing-with-rabbitmq-kc7>

<figure>[![](img/3943163e384cd0365609d75779366833.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jDO52i1N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8hyxydivlOPiaI_VuwarBg.jpeg) 

<figcaption>原于[https://www.flickr.com/photos/criminalintent/3226840700/](https://www.flickr.com/photos/criminalintent/3226840700/)(CC BY-SA 2.0)</figcaption>

</figure>

#### 问题

您的消费者无法处理收到的消息，您需要拒绝它。如果您拒绝 requeue 为真的消息，它将立即重新交付给您的消费者，导致非常高的工作量，因为您的消费者将拒绝该消息。事情又回到了原点😋

基本上，您想要拒绝消息并重新排队，但是有一个延迟。

#### 解

您不能拒绝该消息，但是您可以将它从您的消费者传递到另一个队列，并编写一个 cronjob 将该消息移回工作队列。但是应该有一个更好的解决方案，并且应该可以在不对您的消费者或消息代理进行任何更改的情况下实现。

#### 实解

死信交换 (DLX)来救援了！

对于每个队列，您可以指定一个 DLX，这是被拒绝或删除的消息将被传递到的地方。与 TTL 结合使用，您可以自动执行队列间的延迟传递。

要实现这一点，请按如下方式设置您的交换和队列:

*   创建一个名为`stuff`的交换，并直接绑定到队列`stuff`
*   创建一个名为`stuff-retry`的交换，并直接绑定到队列`stuff-retry`
*   创建一个名为`stuff`的工作队列，将`x-dead-letter-exchange`头设置为`stuff-retry`
*   创建一个名为`stuff-retry`的工作队列，将`x-dead-letter-exchange`头设置为`stuff`，将`x-message-ttl`头设置为`300000`

就是这样。如果您在`stuff`上收听的消费者现在拒绝了一条没有重新排队的消息，那么这条消息将被发送到`stuff-retry-exchange`，而后者将这条消息传递给`stuff-retry-queue`。在该队列中五分钟后，消息死亡，并将被重新交付给`stuff-exchange`，T3 将消息传递给`stuff-queue`，以便您的消费者可以处理该消息。