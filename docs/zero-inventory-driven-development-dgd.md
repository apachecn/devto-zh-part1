# 零库存驱动的发展

> 原文：<https://dev.to/dev3l/zero-inventory-driven-development-dgd>

**思想实验**:

> 给定您当前的产品团队，将一个提交，甚至一个 noop 声明交付到产品中需要什么？这是您团队的*最小周期时间*...那么，为了将最短周期时间减少到一小时以内，您今天需要做些什么呢？您的产品团队如何持续实现零库存，始终安全地将最高价值的工作立即交付给客户？
> 
> *   零库存驱动的发展

# 简介

我对[软件工艺](http://manifesto.softwarecraftsmanship.org/)、敏捷和[精益创业](http://www.thestartupway.com/)原则充满热情。
[敏捷宣言](http://agilemanifesto.org/)表达了*个人和互动*以及*客户协作*的重要性。
精益启动主要是利用[持续改进](https://en.wikipedia.org/wiki/Kaizen)、[减少瓶颈](https://leankit.com/learn/kanban/lean-flow-metrics/)和[经验证的学习](https://en.wikipedia.org/wiki/Validated_learning)来提高产量。
最后，工艺就是永远测量两次，切割一次。Michael Feathers 将 **[遗留代码](https://en.wikipedia.org/wiki/Legacy_code)定义为没有测试的代码**。*不要当驴，不要写遗留代码*...

将会有很多链接，我鼓励你阅读每一个的内容，或者至少假设我不是在胡说八道，没有人相信这是可能的...

# 流程要求

### 1。基于主干的开发

如果你不熟悉基于[主干的开发](https://trunkbaseddevelopment.com/)，请点击链接阅读，我会等待。这种知识推动了零库存驱动的开发。*成功的公司都这样做！你也可以！*

我还没有专业地体验过这种风格的软件交付。像一个书呆子一样，我梦想着这条路在我现在和以前的雇主那里会是什么样子。在我担任过的任何职位上，都没有技术障碍阻止这种软件开发方式...

### 2。整个团队

遵循[整体团队](http://istqbexamcertification.com/what-is-whole-team-approach-in-agile-methodology/)方法的自治团队包含了拥有他们管理的[社会技术系统](https://medium.com/@johnpcutler/the-product-system-a5bbd83424a6)的整个生命周期所必需的所有技能，或者至少是其中的一小部分。**没有‘非我职责’这个概念，它不可能存在**。这种心态可能是被开除出团队的理由。传递价值永远是每个人的头等大事。

这是两个真相中最难的一个。在你自己的思想实验中，我保证这是大部分的工作/阻力。围绕内部团队政治、官僚中层管理、关注点分离的组织信念和“过程”进行谈判需要更多的时间...而不是设置 ci/cd 管道和部署安全代码。

# 技术要求

### 适当的 CI/CD 管道

我想你们公司已经走到这一步了。至少按钮部署...但是如果我们有“按钮”部署，为什么当它是绿色的时候我们必须按下按钮... *<...*

### 零停机部署

你能在不中断服务的情况下自动向(某些)最终用户交付代码吗？对此有很多策略。我最喜欢的两个是蓝绿色部署和 T2 金丝雀释放。我相信还有很多其他的。

### 实时功能切换

这里有一篇关于[功能切换](https://martinfowler.com/articles/feature-toggles.html)的惊人博文。这个简单而**高明的**技巧让我们能够将交付与发布分开。延迟一个特性的发布可能有商业上的原因，但是**团队之外的任何人都不能决定交付**...

将它们绑定到数据库表(或者在运行时可以改变的东西)。就它们的使用达成共识。正确的默认状态是“关闭”...在标志检索错误的情况下，向用户提供当前的系统行为。保持简单。

### 遥测

如果你不能记录它，你就不能测量它。如果你不能衡量它，你不在乎...用您选择的语言找到一个异步聚合日志记录器，把它放在一个索引的可搜索 gui 中，比如 ELK (Elastic) Stack。在硬件和网络层监控基础设施。

这是构建-测量-学习循环的基础。如果堆栈中每一层的遥测都不是您熟悉的概念，请阅读 [devops 遥测-基础知识](https://www.sumologic.com/devops/devops-telemetry-feedback/telemetry-basics/)。首要原则...

# 执行

> 本作品中出现的所有人物和公司或机构都是虚构的。任何与真人的相似之处，无论是活着的还是死去的，纯属巧合。

我的团队目前的最小周期时间在不同的项目之间有所不同，从 9 天到 23 天不等。

我要做的第*件事*是从我的**执行赞助商**那里得到保证，我有一把神奇的红头文件剪子。没有这个，我的时间就浪费在为试图向客户传递价值而道歉上了。

第二，是**说服团队**。我已经预先设定了这个想法。我已经从另外五个人中的一个那里买了。我预料会有阻力。*简直是他妈的情绪杀手*。作为团队的领导者，我将要求尝试一个实验...我们用这种方式编程一次迭代。

> “我们不能把这些人视为坏人，我们必须认真对待他们的反对和怀疑，并想办法帮助他们支持转型，而不是阻碍转型。”
> 
> *   埃里克·里斯的《创业之路》

第三，我的**技术要求**被断言为真理。

我在批处理环境中工作。金丝雀方法将是我的第一个任务。我们已经有了两个[关键性能指标](http://andrewchen.co/know-the-difference-between-data-informed-and-versus-data-driven/)(KPI)，我们目前可以测量*每秒工作单元吞吐量*和*每秒接收字节数*。客观地说，我可以在金丝雀服务器上测量这两个项目，并随着性能的提高向外推动变化。CI/CD 渠道的完善将始终是首要任务。我们已经有功能切换了！

**以身作则执行**。我已经这样发展了一年多了。我愿意相信我发出的每个拉取请求中的每个提交都可以交付到产品中。我遵循一个简单的过程... *tdd、引脚测试、功能切换、功能代码、验证、清理*

把所有人聚集在一个房间里。让他们看看这种工作方式是什么样的。单件流动，直到每个人都感到舒适。我是一个工艺迷。我希望在和我讨论如何安全地在一个单一的遗留代码库中工作之前，我可以命令某人阅读[使用遗留代码](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)和[重构](https://martinfowler.com/books/refactoring.html)。

> 就我目前的信念而言，XP 原则与 TDD 相结合，可以通过 CD 实时安全地执行最困难的生产环境中的复杂特性。#zidd