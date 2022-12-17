# 使用 Kafka 作为主干的分级反应式数据管道

> 原文：<https://dev.to/lepovirta/staging-reactive-data-pipelines-using-kafka-as-the-backbone-13d3>

前阵子，我用 [Kafka](https://kafka.apache.org/) 做了一个关于分段反应式数据管道的演示。这里是[2016](https://www.youtube.com/watch?v=lMlspFnfHM8)[反应峰会](https://www.reactivesummit.org/)上的视频和[幻灯片](https://speakerdeck.com/jkpl/staging-reactive-data-pipelines-using-kafka-as-the-backbone)。我还在 2016 年技能问题大会上发表了同样的演讲。

[https://www.youtube.com/embed/lMlspFnfHM8](https://www.youtube.com/embed/lMlspFnfHM8)

Kafka 已经成为可靠和可扩展的大容量数据分发的事实上的平台。然而，作为一名开发人员，在交付高可用性和交付保证等服务质量的同时，找出与 Kafka 交互的最佳架构和消费模式可能是一项挑战。也很难理解 Kafka 中可用的各种流模式和消息传递拓扑。

在本次演讲中，我们将展示我们在生产中成功采用的模式，并为其他开发人员提供工具和指南，以选择最适合给定数据处理问题的模式。该演示的要点是:构建反应式数据管道的模式、高可用性和消息传递保证、应用程序消费者的群集、主题分区拓扑、偏移提交模式、性能基准，以及[自定义反应式、异步、非阻塞 Kafka 驱动程序](https://github.com/cakesolutions/scala-kafka-client)。