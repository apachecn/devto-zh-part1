# 元数据库简介

> 原文：<https://dev.to/peterfication/introduction-to-metabase>

我想，当你创办一家公司时，建立商业智能(BI)解决方案并不是大多数创始团队要做的第一件事。现在，我已经过了实施这种解决方案的阶段，我认为应该在早期实施这种解决方案，以建立一种衡量一切的文化。

它没有从一开始就在我的公司直接实施的主要原因是没有人有好的解决方案的经验。因此，报告是在原始 SQL 中针对数据库手动执行的，或者是在内部管理区域中实现的，以使它们更容易访问(当然没有图表，因为这会花费太多时间)。正如您可能猜到的，这是一些工作，不能快速更改或扩展。

2015 年秋天， [AWS QuickSight 公布](https://aws.amazon.com/blogs/aws/amazon-quicksight-fast-easy-to-use-business-intelligence-for-big-data-at-110th-the-cost-of-traditional-solutions/)。对我们来说，这似乎是一个非常好的解决方案，因为我们的数据库托管在 AWS 上。2016 年 8 月，我们获得了预览版。它不是很容易使用，用户管理也不是很好。报告和数据源没有在不同用户之间共享，或者我没有找到正确的方法。在大多数情况下，我们使用 uuid 作为主键，AWS QuickSight 当时不支持 uuid。这导致我没有花更多的时间和精力使用 AWS QuickSight。当 [AWS QuickSight 终于发布](https://aws.amazon.com/blogs/aws/amazon-quicksight-now-generally-available-fast-easy-to-use-business-analytics-for-big-data/)时，我又看了一眼，但我仍然对它的外观和感觉不满意。(这非常主观，如果我今天再试一次，可能会有所不同！)

在 AWS QuickSight 发布的同时，我在 [fotograf.de](https://www.fotograf.de) 、 [Marco Beinbrech](https://github.com/beinbm) 的前 CTO 向我展示了他们的 BI 解决方案，一款名为 [Metabase](http://www.metabase.com/) 的开源软件。他告诉我，这是超级简单的设置和易于使用的所有团队成员(特别是非技术团队成员ðÿ˜‰).

的确，他是对的。元数据库提供了不同的方法来将其部署到任何地方。当时我们的大部分服务器都在 Heroku，Metabase 提供了一键部署到 Heroku 的功能。相当整洁！但当时我们也在进行我们与 Kubernetes 的第一步，Metabase 似乎非常适合与 Kubernetes 一起玩。所以我们为它编写了 Kubernetes 对象，瞧，它已经启动并运行了。一点也不麻烦！

## 进一步设置

*设置元数据库时应该考虑的一些事情*

使用生产 PostgreSQL 用户会产生安全风险，因为该用户可以更改数据库中的数据。由于元数据库仅用于报告，因此读取数据，我们为生产数据库创建了一个只读 PostgreSQL 用户。

让元数据库直接访问您的生产数据库并不是一个好主意。当您创建庞大而缓慢的查询时，它将直接影响您的生产服务器。幸运的是， [AWS RDS 使得创建生产数据库的读取副本变得非常容易。](https://aws.amazon.com/rds/details/read-replicas/)

元数据库需要一个可以读写的数据库来管理用户、报告等数据。为了在 Kubernetes 部署中保持元数据库的持久性，我们在 RDS 实例上创建了另一个数据库。这样，我们当前的备份过程也备份了元数据库数据。您只需向 Kubernetes 中的元数据库部署提供 PostgreSQL 连接细节，它就能开箱即用。然后，您可以创建和重建集群，而不会丢失任何元数据库内部数据。

我们公司使用 Google Apps，Metabase 内置了 Google OAuth 登录。因此，你只需按照指南操作，你的所有同事就可以注册他们的谷歌应用套件账户。

用户管理可以通过用户组和集合(元数据库中报表的文件夹)来处理。Marco 告诉我如何根据部门结构建立收藏和用户组。这样，您可以非常容易地在集合级别上实现用户访问。

## 第一步

我非常推荐[阅读元数据库](http://www.metabase.com/docs/v0.23.1/)的文档。他们真的很棒，尤其是第一步。

在元数据库中，报告被称为问题。因此，为了创建第一个报告，您单击`New Question`，选择数据库，选择表格，添加过滤器，指定您想要查看的内容(例如，用户计数)并添加分组(例如，created_at by month)。所有这些都在一个简单的点击界面中，可供许多用户使用，而不仅仅是公司中的 SQL 专业人员。(它仍然允许您创建定制的 SQL 查询)。

*我喜欢！*

最后，你可以把多个问题放在一个仪表板上，对公司的情况有一个很好的概述。

## 结论

总而言之，Metabase 对于技术团队来说非常容易设置。如果我们用了 Heroku，那就更容易了。最重要的是，对于所有团队成员来说，使用起来都非常简单和有趣。ðŸ˜Š

*谢谢 Marco，这里的所有提示*ðÿ˜š*在以后的帖子中，我将详细介绍我们如何在 [store2be](https://www.store2be.com) 创建高级报告。*

* * *

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*