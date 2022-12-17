# 为您的企业移动应用构建大数据后端的 3 大挑战

> 原文：<https://dev.to/jessaminmorgan/3-challenges-of-building-a-big-data-backend-for-your-enterprise-mobile-app-76m>

[![Big Data Backend for Your Enterprise Mobile App](img/127221dda971344808ef078bc58d30d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bvSLdVEF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4i5g2t66hsclq1ux5pmq.png)

大多数应用程序通过提供互联网接入并向用户提供关于他们的问题和行动的实时反馈来带来价值。很少有企业应用是独立的，不需要互联网连接。这些仅仅是静态信息的显示。这就是为什么值得考虑后端和 API 来丰富企业应用程序功能的原因。

## 了解后台的黑暗世界

对于应用程序开发人员来说，后端可能会令人生畏，因为它需要一套不同的技能来处理受加密和安全规则控制的服务器和数据库。在处理大数据时，甚至还有更多要求，比如与处理 Hadoop 框架相关的要求。

### 委托给服务器

由于与服务器相比，移动设备的资源有限，所以最好将所有计算工作转移到一台强大的机器上，并且只将结果传达给用户。对于开发人员来说，这有助于为不同的平台构建应用程序，并在最后集成它。

### 计划进行离线备份

用户在使用该应用程序时可能会遇到信号弱的问题或根本没有互联网连接。一个设计良好的软件可以创建本地数据备份，并在链接可用时与服务器重新同步。如果该应用程序可以处理大数据，离线存储的数据量不能超过设备的容量，这一点非常重要。

### 解决同步问题

在大数据项目中，由于“速度”维度，同步推动质量并有助于解决数据冲突。经验法则是根据时间戳优先考虑数据集的最新版本，并确保解决时区差异。

Itranstion 的企业移动应用程序开发内部专家表示，移动应用程序的同步应考虑设备的固有限制，如电池寿命、数据传输成本和信号功率。此外，该操作应仅在达到特定阈值时执行，以避免同步或信号丢失期间的功耗。

## 选择合适的查询工具:SQL vs. NoSQL

在过去的 40 年中，SQL 成为数据库查询的标准，因为它允许对相同的信息集进行多次查询，以适应用户的需求。它有着[悠久的历史](https://docs.oracle.com/cd/B12037_01/server.101/b10759/intro001.htm)，并且已经集成到多个工具中。这是一个关系工具，主要缺点是它通过向服务器添加更多资源来进行垂直扩展。

另一方面，由于大数据和互联网巨头如脸书和 LinkedIn，NoSQL 变得很受欢迎。NoSQL 在灵活性方面非常出色，因为它背后没有固定的模式，而是可以根据需要不断发展并获得新的属性。它是为半结构化数据设计的；因此，通过跨服务器分布数据，可以轻松地进行水平扩展。

SQL 是声明性的，用户需要做的唯一事情就是请求正确的信息，它会以系统中已经存在的形式得到它。相比之下，NoSQL 作为一个程序性查询，要求用户指定答案的最终类型。由于在[过程化方法](https://www.networkworld.com/article/2226514/tech-debates/what-s-better-for-your-big-data-application--sql-or-nosql-.html)中，程序员每次都必须重新设计系统，因此成本更高，用户友好性更差。

为了克服这个问题，除了 Hadoop 最初的查询工具 Hive 之外，还有一些 SQL 实现可以查询 HDFS 系统。

## 修复安全问题

诚然，一个应用程序和它的代码一样安全，但它也和后端一样安全，有时完全由供应商控制。最近的一项研究表明，来自 1，000 多个应用程序的超过 43TB 的企业数据被暴露，并且容易被窃取个人数据。这个问题被戏称为[“病号服”](http://info.appthority.com/hubfs/website-LEARN-content/Appthority%20Q2-17%20Threat%20Report%20HospitalGown.pdf)漏洞。这个问题变得更糟，因为公司无法检测到违规行为，直到为时已晚，正如勒索软件和网络钓鱼试探所表明的那样。

该报告强调，尽管在应用程序和数据存储之间设置了适当的防火墙，但主要问题是数据存储供应商和互联网之间缺乏安全项目。

## 安全问题的解决方案

第一步是创建贵公司使用的所有应用程序以及每个应用程序拥有的权限的清单。简单的电子表格就可以了。更新到最新版本，并删除所有不再需要的应用程序。

其次，确保您与供应商就数据存储和保护方式达成一致。后端必须是安全的，信息必须使用最新的标准加密。最后，确保来自服务器的数据是最新的，并且没有被破坏。

## 后端即服务(BaaS)是可行的解决方案吗？

如果开发者不想处理后端带来的问题，BaaS 意味着你外包实现，通过 API 和 SDK 连接到厂商提供的环境。在这种情况下，您将架构、速度和安全性的责任完全委托给您的供应商，让您有时间专注于核心能力。

好消息是，这些服务大部分都是建立在免费增值模式上的，你可以随着自己客户群的增长而扩展。不利的一面是，BaaS 可能会限制您的选择，并提供太低的定制选项。最近的案例 [Parse 停止其服务](https://code.tutsplus.com/articles/back-end-as-a-service-for-mobile-apps--cms-28154)也是一个值得注意的警示信号。更换提供商可能成本高昂，涉及重新设计、可能的停机时间和客户流失。

## 最后的想法

构建一个快速、安全、可靠的企业移动应用需要的不仅仅是设计和 UI/UX 技能。能够处理大数据的发电站是后端，它应该遵循安全性和数据保护的最佳实践，为可扩展性而构建。该架构必须解决查询类型、同步问题，并适应不同类型的数据，包括半结构化和非结构化数据。

由于企业信息的敏感性，数据安全性应该是开发人员的首要任务。对供应商系统的盲目信任会产生不良后果。