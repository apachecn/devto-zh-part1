# 卡夫卡用法——技巧和提示

> 原文：<https://dev.to/gimlet2/kafka-usage--tipshints-49g0>

今天我们就来聊聊[阿帕奇卡夫卡](https://kafka.apache.org)。你可能已经知道，Kafka 是“一个分布式流平台”，所以它可以用作队列、消息总线等。

但是这个故事不是关于卡夫卡本身的。这是关于我最近在开发一个基于 Kafka 的服务时遇到的问题。小应用程序使用来自已定义主题的消息，并根据模式验证它们。一点也不像火箭科学。酪

1.  提前检查 Kafka 服务器的版本——即使是微小的版本差异也会破坏 API。例如，服务器版本 0.10.0.1 与客户端版本 0.10.1.1 不兼容。注意就好。
2.  检查您的 Kafka 服务器上使用的压缩机制。支持 [GZip](https://www.gnu.org/software/gzip/) 和 [Snappy](https://google.github.io/snappy/) 。对我来说是爽快的，你知道吗？它需要 [glibc](https://www.gnu.org/software/libc/) 。我的基本码头形象是阿尔卑斯山。Alpine 没有 glibc 的支持。所以，选择正确的基础图像。
3.  如果您使用 Kafka Streams API，并希望根据某个谓词将一个流分成两个流，那么您可以将该谓词与总是返回 true 的谓词一起传递给`_branch_`方法—某种 _ everithingeseprediate(_ 谓词{ _，_ - > true })。
4.  kafka-控制台-消费者和 Kafka-控制台-生产者-您调试 Kafka 应用程序的最好朋友。
5.  如果你打算扩展你的应用程序来并行处理某个主题，不要忘记增加该主题的分区数量。但是请记住，只可能增加分区数量，但没有回头路。

这就是我想提的。