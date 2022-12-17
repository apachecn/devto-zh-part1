# 使用 Amazon Web Services 扩展项目

> 原文：<https://dev.to/dianamaltseva8/scaling-the-project-with-amazon-web-services-e81>

### 亚马逊网络服务

AWS 是一个云服务平台，提供各种基础设施服务，包括云计算、访问数据库和数据存储以及其他功能，帮助应用程序开发人员成功扩展他们的应用程序。

与内部计算相比，使用云计算的成本更低。由于成千上万的客户一起消耗云中的资源， [AWS](https://aws.amazon.com/?nc1=h_ls) 可以实现高度的规模经济，帮助组织降低使用资源的费用。

### 缩放项目:场景

**1 用户**

 **在这种情况下，您只需要一个用户。要构建这个架构，您应该在单个数据库上运行，例如 t2.micro 类型的数据库。

实例的类型包括 CPU、存储、内存和网络容量的各种组合，为应用开发团队提供了选择最佳资源组合的灵活性。

使用单个实例，您将能够运行整个 web 栈，包括(例如)web 应用程序、数据库、管理等等。

然后，对于域名系统(DNS ),最好使用 Amazon Route 53，这是一个高度可用和可扩展的基于云的 DNS web 服务。

Amazon Route 53 服务用于仅连接到“健康的”地址(使用 DNS 检查),并独立监控应用程序及其端点的状态。

**> 10 个用户；> 100 名用户**

未来的网站使用一个主机。将单个主机分离成多个主机，因为使用分离的主机将使网站和数据库能够彼此独立地扩展和发生故障。

此外，对数据库使用一台主机。从 SQL 数据库开始，只有在必要时才转移到 NoSQL。你应该从 NoSQL 开始，当:

*   您有一个超级密集的数据工作负载。
*   你没有任何关系数据。
*   该项目需要高产量。
*   未来应用程序的要求是非常低的延迟。
*   该系统在第一年需要存储超过 5 TB 的数据。

要将您的 web 项目扩展到超过 100 个用户，请为 web 层使用单独的主机，并将数据库存储在 Amazon 关系数据库服务(Amazon RDS)上。

Amazon AWS 是一个 web 服务，它简化了在云中设置、操作和扩展关系数据库的过程。此外，Amazon RDS 支持管理常见的数据库管理任务。

了解有关通过 AWS 将 **[项目扩展至数百万用户的更多信息。](https://smartym.pro/blog/software-project-development-how-to-scale-your-application-with-amazon-web-services/)T6】**

首先，查看 [AWS 文档。【T2](https://aws.amazon.com/documentation/?nc2=h_ql_d)**