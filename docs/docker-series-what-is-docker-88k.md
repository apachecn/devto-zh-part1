# 【docker 系列】docker 是什么？

> 原文：<https://dev.to/hbibzdeploy/docker-series-what-is-docker-88k>

[![docker logo](img/0996df5c85f25318f129c74290846b6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ihnpbq_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.docker.com/sites/default/files/Whale%2520Logo332_5.png) 
【穆罕默德·哈比·贾瓦迪/【29-11-17】

大家好，我是 Muhammad Habib Jawady，在这篇文章中，我将讨论一个干净、安全和可移植的运行时环境，叫做——你猜对了——docker。

这将是一个没有任何额外技能的动手系列文章，我将涵盖 docker 的基础知识，以及基本的术语和命令。但是首先，

### 何必在意？

在他们推出后的 4 年里:

[![docker in 2017](img/5aa08eff34f39231d93808aa6799a12f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZeZybaXs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.slidesharecdn.com/docker101-july20171-170714233644/95/introduction-to-docker-2017-14-638.jpg) 
它是一个容器平台，可满足混合云中每个应用的需求。这是一个在 Apache license 2.0 下许可的开源项目，在打包应用程序以部署在 linux 操作系统上时，无需担心库和依赖性。

### 为什么不虚拟化？

许多人想知道为什么不渴望虚拟化。最方便的回答是“为什么不两者都要”？！事实上，它们可以，而且它们都在 tune 中使用，因为大多数 VPS 提供商，如 AWS 和 Azure，都提供基于第 1 类虚拟机管理程序的实例 1(又名。bare-
[![hyperv types](img/7db295186f04dd65981e88f4175e114c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wcHAXanw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.computerperformance.co.img/win8/hypervisor.jpg) 
金属全虚拟化技术(如“Xen”)和 Docker 可以在虚拟化的 Linux 实例上运行，但虚拟机却不是这样，尤其是大多数主流云计算平台尚不支持虚拟化技术

**容器允许在没有虚拟机管理程序的情况下部署项目，并通过消除来宾操作系统层来帮助消除“它在我的计算机上工作”的问题。**

[![docker vs VMs](img/183516d6d90370fe2789d4eca4ab5b13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KklrYXgX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/pG94I.png) 
**Docker 发布？**

Docker 有两个版本:**社区版(CE)** 和**企业版(EE)** 。每一种都有其特性以及更新和修补频率，可在云上和内部的多个平台上使用。

**企业版** ** iii**
|
| - | - |
|

| **稳定更新通道** | **边缘更新通道** |
| --- | --- |
| 每季度为您提供可靠的更新 | 每月为您提供新功能 |

*   给定的 Docker CE 稳定版将在下一个 Docker CE 稳定版发布后的一个月内收到补丁和更新。
*   在后续 Docker CE Edge 或稳定版本发布后，给定的 Docker CE Edge 版本将不会收到任何补丁或更新。|给定的 Docker EE 版本将在发布后至少一年内获得补丁和更新。|

安装请访问:[https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

***1*** 更多关于虚拟化技术:【https://securitywing.com/types-virtualization-technology/】T4

***I***Azure 中的嵌套虚拟化支持:[https://Azure . Microsoft . com/en-us/blog/Nested-virtual ization-in-Azure/](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/)

***ii***【https://www.docker.com/community-edition/】T4

***iii***【https://www.docker.com/enterprise-edition/】T4