# 星期五爆炸#12

> 原文：<https://dev.to/horia141/friday-blast-12-43jk>

[软件工程师的时钟(2017)](http://zipcpu.com/blog/2017/09/18/clocks-for-sw-engineers.html) -硬件设计和软件设计很不一样。它从最基本的*时钟*概念开始，这是一个周期性的方形信号，用于同步设计的所有其他组件。网站上还有一大堆硬件设计技巧。

[构建 ReplicatedLog:高性能复制日志服务(2015)](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2015/building-distributedlog-twitter-s-high-performance-replicated-log-servic.html)-Twitter 的 replicated log 快速概览。该服务类似于 Kafka，但有不同的权衡和较低层次的接口，他们的发布/订阅系统、数据库等都基于该接口。可以建造。有了具有良好抽象和性能的底层系统，构建其他分布式系统就简单多了。事实上，他们的曼哈顿分布式数据库就是建立在这个基础上的。其他系统如 BigTable 依赖于一个可靠的系统，如 Google 文件系统，这使得它们的设计更加简单。相比之下，像亚马逊的发电机这样的东西必须自己照顾一切，导致更复杂的设计。

[堆积下一个平台(2015)](https://www.nextplatform.com/2015/10/16/stacking-up-a-modern-platform/)——描述一个“平台”应该包含什么，以及从它的用户那里提供什么，这显然是一种类似谷歌/脸书/推特的方法。像容器基础设施(运行时管理和注册)，构建系统，日志，负载平衡器等等。这篇文章是 2015 年的，但它很好地预测了这期间发生的事情。唯一没有涉及的是功能即服务/无服务器的“兴起”。

[浮点直观解释(2017)](http://fabiensanglard.net/floating_point_visually_explained/) -这篇文章给了我很大的启发。和作者一样，我也在浮点的定义上挣扎。这篇文章为我澄清了许多问题。基本上，FP 数的指数部分选择一系列数中的一个，而尾数从该系列中选择一个元素。范围呈指数增长，因此在每个范围内分辨率都在下降。请继续阅读更多细节和图片。

[关于浮点计算的基本事实(2016)](https://mortoray.com/2015/07/06/essential-facts-about-floating-point-calculations/) -根据之前的文章更容易理解。核心思想是 FP 数不是实数，所以我们对它们的很多直觉都是错误的。此外，硬件实现可能会咬我们。最明显的例子是 x == y 和 x！= y 同时为假，因为 x86/x64 机器使用 80 位精度的 FP 单元。