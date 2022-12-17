# 星期五爆炸#26

> 原文：<https://dev.to/horia141/friday-blast-26-2cg7>

[了解 C++内存模型(2018)](https://davmac.wordpress.com/2018/01/28/understanding-the-c-c-memory-model/)——简单讨论原子操作和内存障碍，以及它们在 C/C++上下文中的含义(以及许多其他处于同一抽象级别的语言——Java、C#、Rust 等)。原子用于保证某些非常基本的操作作为一个单元发生。像递增、比较和交换、比较和测试等等。特定对象的原子操作集形成了总顺序。然而，这并不意味着其他对象或其他操作的可见性。为此，需要记忆屏障。C/C++有获取和释放障碍的概念，这保证了之前和之后的所有操作都不会被重新排序。障碍与原子性无关。所以你需要两者作为并发原语的基础。

[软件复杂性正在杀死我们(2018)](https://www.simplethread.com/software-complexity-killing-us/) -主要的论点是，现在的业务真的很复杂。有许多软件需要做一些事情，然后这些软件之间有许多相互联系。思考这样的事情- AD 需要从人力资源系统了解新加入公司的人。OSS 在为软件提供构建模块方面做得非常好，所以我们不需要重新实现 OS/核心库/UI 框架/数据存储等等。但高于这个数字就没什么可做的了。这就是商业环境中许多复杂性的来源。

[地形 1Password (2018)](https://blog.agilebits.com/2018/01/25/terraforming-1password/) -讲述 1Password 如何将其基础设施配置从[云形成](https://aws.amazon.com/cloudformation/)切换到[地形](https://www.terraform.io/)的故事。作为代码，两者都是基础设施的优秀工具，但 Terraform 是更好的工具。根据我的经验，情况也是如此——编写 YAML 和 Terraform 的抽象比编写 JSON 和 CF 的要好。

[创始人基础设施指南(2017)](https://medium.com/starting-up-security/an-infrastructure-guide-for-founders-bbada8431fb1) -这并不像标题那样宽泛，但仍然是相关的。基本上，你应该从一开始就考虑如何处理应用程序日志&监控、AWS/GCP 账户的结构以及围绕基础设施的软件工程。常识性的建议。