# 流沙之上的建筑:综述

> 原文：<https://dev.to/shubheksha/building-on-quicksand-a-summary-9ke>

让我们试着分解一下帕特·海兰德和大卫·坎贝尔在 2009 年发表的论文“在流沙上建造”[。所有引用都来自报纸。](http://arxiv.org/ftp/arxiv/papers/0909/0909.1788.pdf)

本文重点介绍了大型容错复制分布式系统的设计，以及它是如何根据不断变化的需求而发展的。它从“可靠的系统总是由不可靠的组件构建而成”开始。

> 随着不可靠组件粒度的增长(从镜像磁盘到系统再到数据中心)，与备份进行通信的延迟变得令人不快。这导致了一个更宽松的容错模型。主系统将确认工作请求及其动作，而不等待确保备份被通知该工作。这提高了系统的响应能力，因为用户不会因为与备份的缓慢交互而延迟。

容错系统可以由许多组件组成，它们的目标是在其中一个组件出现故障时保持正常运行。在这次讨论中，我们不考虑 ***拜占庭*** 故障，而是考虑 ***快速故障*** 模型，在这种模型中，一个组件要么正常工作，要么出现故障。

本文继续比较 Tandem NonStop 系统的两个版本——一个使用同步[检查点](https://en.wikipedia.org/wiki/Application_checkpointing),另一个使用异步检查点。请参考论文的第 3 部分了解所有细节。我想简单谈谈这两种检查点策略之间的区别。

*   同步检查点:在这种情况下，每次写入主服务器时，都需要将状态发送到备份服务器。只有在备份确认写入后，主节点才向发出写入请求的客户端发送响应。这确保了当主服务器出现故障时，备份服务器可以接管而不会丢失任何工作。
*   异步检查点:在这种策略中，主节点一处理完写操作就确认并提交它，而不需要等待备份节点的回复。这种技术改善了延迟，但也带来了其他挑战，这些挑战将在后面讨论。

#### 日志运送

> 传统的数据库系统有一个读取日志并将其发送到备份数据中心的过程。这种机制的正常实现是在主系统上提交事务(确认用户的提交请求)并异步发送日志。备份数据库重放日志，不断地追赶。

上述机制被称为日志传送。这带来的主要问题是，当主服务器发生故障，备份服务器接管时，一些最近的事务可能会丢失。

> 这必然会打开一个窗口，在此窗口中，工作已向客户端确认，但尚未发送到备份。在此窗口期间，主服务器出现故障会将主服务器内的工作锁定一段未知的时间。备份将在不知道锁定工作的情况下继续进行。

在系统中引入异步在延迟、响应时间和性能方面具有优势，但是它使得系统更容易在主系统出现故障时丢失工作。有两种方法可以解决这个问题:

1.  当工作失败时，丢弃主数据库中锁定的工作。系统能否做到这一点取决于需求和业务规则。
2.  拥有一个恢复机制，以便在备份恢复时将主数据库与备份数据库同步，并重试丢失的工作。只有在操作可以以幂等的方式重试并且无序重试是可能的情况下，这才是可能的。

该系统失去了作者所谓的“权威真理”的概念。如果允许将工作锁定在不可用的备份或主系统中，没有人知道系统在任何给定时间点的准确状态。

这导致了一个结论，即在具有异步检查点的系统中的业务规则是概率性的:

> 如果主节点使用异步检查点，并对传入的工作应用业务规则，那么它必然是一个概率规则。尽管初衷是好的，但初选无法知道它还能活着执行商业规则。当参与执行这些业务规则的备份系统异步绑定到主系统时，这些规则的执行不可避免地变成了概率性的！

作者指出，只要保留业务规则，交换操作(可以重新排序的操作)可以被允许独立执行和重新排序。然而，这对于存储系统来说很难做到，因为写操作是不可交换的。

另一个考虑是，单个操作的工作是等幂的，即，执行该操作任何次数都应该导致系统的相同状态。

> 为了确保这一点，应用程序通常会为作品分配一个唯一的编号或 ID。这是在系统入口处分配的(即首先处理工作的副本)。当工作请求在网络上发出响声时，副本很容易发现它已经看到了该操作，因此不会重复执行该工作。

作者认为，系统中的不同操作可以根据业务需求提供不同的一致性保证。一些操作可以选择传统的一致性而不是可用性，反之亦然。

接下来，作者认为，一旦系统中没有权威真理的概念，所有的计算都可以归结为三件事:记忆、猜测和道歉。

1.  回忆:你只能希望你的复制品记得它已经看过的东西。
2.  猜测:由于只有部分知识可用，副本基于本地状态采取行动，可能是错误的。"在任何允许绝对真理退化的体系中，任何行动充其量都是一种猜测。"在这样的系统中，任何行动都有很高的成功概率，但这仍然只是一种猜测。
3.  道歉:错误是不可避免的，因此每个企业都需要通过人工干预或自动化来建立道歉机制。

接下来，本文以使用 Dynamo &一个结算支票的系统构建的 Amazon 购物车为例，讨论最终一致性的主题。进入这些系统的工作被唯一地标识，并由单个复制品处理。当连接允许时，它流向其他副本。进入这些系统的请求是可交换的(可重新排序的),可以在不同的副本上以不同的顺序进行处理。

> 单靠存储系统无法提供我们创建健壮的异步检查点系统所需的交换性。我们需要商业运作重新排序。亚马逊的发电机不会自己做到这一点。Dynamo 存储系统之上的购物车应用程序负责最终一致性和交换性的语义。作者认为，现在是我们超越对更新和存储系统的最终一致性的检查的时候了。真正的行动是在检查基于应用程序的操作语义时。

接下来，他们讨论了在可能无法相互通信的副本中分配资源的两种策略:

1.  过度供应:资源在副本之间划分，这样每个副本都有一个固定的资源子集可以分配。没有副本可以分配实际上不可用的资源。
2.  超额预订:资源可以单独分配，无需确保严格的分区。这可能会导致副本分配并不真正可用的资源，承诺一些他们无法交付的东西。

本文还讨论了一种被称为“座位预订模式”的东西，这是过度供应和过度预订之间的一种折衷:

> 任何在线购票的人都会认识到“座位预订”模式，在这种模式下，您可以确定潜在的座位，然后在有限的时间内(通常是几分钟)完成交易。如果在该时间段内未成功完成交易，座位将再次被标记为“可用”。

#### 酸 2.0

ACID 的经典定义代表“原子的、一致的、隔离的和持久的”，它的目标是让应用程序认为有一台计算机在处理事务时不做任何其他事情。作者讨论了 ACID 的一个新定义，它代表:结合的、交换的、幂等的和分布的。

> ACID2.0 的目标是，如果工作的各个部分都发生了，那么它就是成功的:至少一次，在系统中的任何地方，以任何顺序。这定义了一种新的一致性。各个步骤发生在一个或多个系统中。应用程序明确地容忍工作无序发生。它还允许工作在每台机器上发生多次。

按照 ACID 的经典定义，容错基于线性历史。如果我们想在一个分布式系统中实现同样的保证，它将需要“往往很脆弱”的并发控制机制。

> 当应用程序受限于交换性和结合性的额外需求时，世界变得容易多了。不再需要跨故障单元以同步方式对状态进行检查点检查。相反，有可能对信息的共享非常懒惰。这导致了离线、慢速链接、低质量数据中心等等。

总之，

> 我们试图描述当今许多应用程序在处理广泛分布的系统中的故障时所使用的模式。工作的可重新排序性和工作的可重复性对于在混乱的分布式世界中成功执行应用程序是至关重要的，在这个世界中，系统可以随心所欲地来来去去。

附:如果你已经做到了这一步，并且想在我发表这些帖子的时候收到邮件，请在这里注册。