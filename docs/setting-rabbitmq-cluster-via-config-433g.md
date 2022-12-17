# 通过配置设置 RabbitMQ 集群

> 原文：<https://dev.to/dzeban/setting-rabbitmq-cluster-via-config-433g>

RabbitMQ 是最受欢迎的 AMQP 代理，或者换句话说，是队列服务器。人们使用它来排队和跟踪繁重的处理，在工人之间分配任务，缓冲传入的消息以处理高峰和许多其他用例。

这听起来像是基础设施中非常重要的一部分，所以你最好让它高度可用，RabbitMQ 对这种情况有集群支持。

现在有两种方法可以创建 RabbitMQ 集群。一种是用`rabbitmqctl join_cluster`手工制作，如文档中描述的[。另一个是通过配置文件。](http://www.rabbitmq.com/clustering.html#transcript)

我还没有在任何地方看到后一种情况，所以我会在这篇文章中自己做。

我将在这里描述的大部分事情都是在我的 [rabbitmq-cluster 角色](https://github.com/dzeban/rabbitmq-cluster)中自动完成的。

假设您已经在 3 个节点上安装了 RabbitMQ 服务器。它已经开始，现在你有 3 个独立的 RabbitMQ 实例。

要使它成为一个集群，首先要停止所有 3 个实例。您必须这样做，因为一旦设置好，RabbitMQ 配置(包括集群)将持久保存在 mnesia 文件中，并将尝试使用自己的内部工具构建一个集群。

让它停下来，你必须像这样清除记忆的基本目录。同样，您需要这个来清除任何以前的配置(通常是由于以前失败的尝试而中断的)。

现在是它的肉。在每个节点上打开/etc/rabbitmq/rabbitmq.config 并添加集群节点列表:

```
{cluster_nodes, {['rabbit@rabbit1', 'rabbit@rabbit2', 'rabbit@rabbit3'], disc}}, 
```

Enter fullscreen mode Exit fullscreen mode

接下来，再次在每个节点上，创建文件/var/lib/rabbit MQ/. Erlang . cookie 并向其中添加一些字符串。它实际上可以是任何东西，除非它在集群中的所有节点上都是相同的。此文件必须具有 0600 权限和所有者，rabbitmq 服务器进程组。

现在我们已经准备好启动集群了。但是坚持住。要使它工作，您必须逐个启动节点，而不是同时启动。因为否则不会创建集群。这是我在邮件列表[这里](http://rabbitmq.1065348.n5.nabble.com/Rabbitmq-boot-failure-with-quot-tables-not-present-quot-td24494.html#a24512)中发现的一些奇怪问题的解决方法。

我遇到过两次——一次是当我在同步窗格中通过 tmux 配置 RabbitMQ 节点时，另一次是当我在编写 Ansible role 时。

但是最后，我得到了一个非常好的集群，它具有合理的生产配置值，您可以在我的角色的[默认值中查看。](https://github.com/dzeban/rabbitmq-cluster/blob/master/defaults/main.yml)

就是这样。下次见！