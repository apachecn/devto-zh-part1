# 开发软件项目架构:结构、工具和技术

> 原文：<https://dev.to/dianamaltseva8/developing-a-software-project-architecture-structure-tools-and-technologies-4of>

软件项目开发是一个复杂的具有挑战性的过程，涉及到不同的阶段。它还有一个典型的架构，包括后端和前端，需要使用各种各样的工具和技术。

### 1.后端

**1.1 服务器基础设施**

每个项目通常有两个基础设施:

*   开发(试运行)
*   生产(现场)

每个配置都有[【VPC】](https://en.wikipedia.org/wiki/Virtual_private_cloud)用子网和路由表建立安全和资源隔离。

在我们的项目中，我们使用由 [亚马逊网络服务提供的以下服务和能力:](http://searchaws.techtarget.com/definition/Amazon-Web-Services)

*   亚马逊 VPC——提供在亚马逊创建虚拟私有云并隔离所有资源的能力。

*   Amazon EC2——是一种为不同目的提供服务器实例的服务。

*   Amazon ECS——在 Amazon 基础设施中提供 Docker 容器编排的可能性。它代表了一个 Amazon 模拟的 Kubernetes 编排工具。

*   Amazon Application Load Balancer——支持在部署到 ECS 集群或 EC2 实例的多个 Amazon 应用实例之间自动分配传入的应用流量。

*   Amazon Auto Scaling——帮助维护应用可用性，并允许根据定义的条件(CPU 或网络流量)自动增加或减少 EC2 实例(带有应用或在 ECS 集群中)的容量。

*   Amazon Lambda——允许在 Amazon 基础设施中使用无服务器方法。该服务可用于执行一些短期任务或 cron 操作。

*   Amazon RDS——包括应用程序的数据库。

*   Amazon elastic cache——该服务提供已经配置好的生产就绪的内存数据库，如 Redis 或 Memcache。

*   亚马逊 S3 -是一种服务，用于所有类型的文件。

*   亚马逊 SQS——代表一个消息队列服务，提供类似于 JMS 的功能。感谢亚马逊 SQS，我们获得了分离应用组件的能力。

*   亚马逊 SNS——是一项主要用于在不同平台发送推送通知的服务。

*   亚马逊 SES -用于发送电子邮件。

*   亚马逊 Route 53 -是一种云 DNS 服务，可以有效地将用户请求连接到基础设施，运行在 AWS 中，如亚马逊 EC2 实例、应用负载平衡器、亚马逊 S3 桶或亚马逊 CloudFront 分发。

*   Amazon Certificate Manager -允许存储任何证书，并将它们连接到 Amazon CloudFront 或 Amazon Application Load Balancer。有了 ACM，开发人员就能够将已经存在的证书上传到这个服务中，或者使用 Amazon 免费生成的证书。

*   Amazon CloudFront -在 Amazon 中提供 CDN 功能。我们可以将这项服务与亚马逊 S3 连接起来，并通过亚马逊提供的一些边缘位置的缓存来分发文件。这项服务还使开发人员能够自动分发文件。

**1.2 后端架构**

一般来说，一个应用程序有一个整体架构，其中包含用于代码解耦的层。

**-储存库层。**这一层有实现数据库通信功能的类，比如通过数据库模型的请求或更新。

**-服务层。**它拥有实现应用业务逻辑、与第三方系统集成、事务处理和缓存的类。

**-控制器层。**这一层有实现 HTTP 请求处理的类。HTTP API 是根据 RESTful 原则实现的，可以处理不同的请求内容类型，比如 JSON、XML 和 protobuf。

了解有关软件项目架构和实践专业知识的 **[技术栈的更多信息。](https://smartym.pro/blog/our-app-development-process-building-a-software-project-architecture/)**T7】