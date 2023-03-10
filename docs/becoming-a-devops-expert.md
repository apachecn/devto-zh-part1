# 成为 DevOps“专家”

> 原文：<https://dev.to/osblaineora/becoming-a-devops-expert>

### 我决定学习更多关于 DevOps 的知识。

我一直相信自动化重复的任务，让机器尽可能多地做“我的”工作。我学习的最好方式是(你可以从我的博客的名字看出来)我了解这个主题，我从我所学到的东西中建立一些东西，并且分享我的经验。

鉴于 DevOps 是一个非常大的话题，要公正地看待它，需要不止一两篇文章。这篇文章是一系列博客文章、视频和演示的第一篇，随着我了解的越来越多，我计划创建这些文章。我认为最好从我“认为”我现在知道的开始。

### 什么是 [DevOps](https://en.wikipedia.org/wiki/DevOps) ？

几年前，我利用 Jenkins 为我正在从事的一个项目创建了一个[连续交付](https://en.wikipedia.org/wiki/Continuous_delivery)管道。我是唯一一个参与这个项目的人，在一次会议上看到 CD 演示后，我想我应该试一试。我一切正常，我对自己很满意。然后我开始听到 DevOps 这个术语，并认为这只是一个术语，用来描述我已经在做的事情。我只说对了一部分。

DevOps 不仅仅是软件交付过程的自动化，它还是一种文化心态。开发人员和运营人员在项目的整个生命周期中协同工作，而不是各自为战。因为我独自做那个项目，所以我错过了这个方面。目前，我没有参与任何可以体验完整文化的项目，所以我计划在学习过程中扮演不同的角色。

如果你想要一个更好的定义，网上有很多来自真正专家的资料。随着我开始了解更多，我只想记录 DevOps 对我的意义。

### 我目前的计划。

我一直在开发一个用于演示和学习的开源应用程序，名为 [DinoDate](https://github.com/oracle/dino-date) 。我将围绕这个应用程序构建一个 DevOps 流程。我将使用 [Oracle 开发人员云服务](https://cloud.oracle.com/developer_service)来构建这个流程，以对抗 [Oracle 云数据库](https://cloud.oracle.com/database)和其他 Oracle 云服务，以及其他工具，如 Jenkins 来对抗虚拟机上的 [Oracle 数据库。](http://www.oracle.com/technetwork/database/enterprise-edition/databaseappdev-vm-161299.html)

##### 计划步骤:

1.  定义按原样手动部署 DinoDate 的步骤。
    1.  自动化当前的构建和部署流程，运行一些脚本并将代码 scp 到一个 [Oracle Compute](https://cloud.oracle.com/compute) 实例，我已经在该实例中设置了 Python 和 NodeJS。
    2.  将 NodeJS 和 Python 应用程序部署到一个 [Oracle 应用程序容器云](https://cloud.oracle.com/acc)实例。
2.  添加一些开源工具来改进这个过程。
    *   使用 Gradle 构建脚本。
    *   使用 Liquibase 的模式对象版本控制。
    *   使用 UTPLSQL 对 PL/SQL 进行单元测试。
3.  从头开始自动创建基础架构(数据库、计算实例)，然后部署、测试和销毁。
4.  使用 Jenkins(或其他工具)在虚拟机上重现整个 CD 管道。

一旦我对工具和工作流的理解感到满意，我会找到一个将从 DevOps 环境中受益的项目，并~~纠缠~~鼓励他们切换到 DevOps 流程，并提供作为 DevOps“专家”的机会。

### 更来了。

请留意这里和我的 YouTube 频道，我会在过程中发布如何操作和“经验教训”的帖子。如果你发现我已经做错了什么，或者你有什么特别的兴趣想让我关注的话，请随意发表评论。

*本帖最初发表于[learncodeshare.net](https://learncodeshare.net/2017/08/31/becoming-a-devops-expert/)T3】*