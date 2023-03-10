# 获得 AWS 几乎不需要任何成本

> 原文：<https://dev.to/tonytalkstech/getting-my-aws-cost-to-nearly-nothing-c78>

一些背景:我在亚马逊网络服务(AWS)中运营几个网站。其中包括我的摄影网站项目，MorrisPhotos.com。这篇文章简要概述了我如何设法每月节省大约 55 美元的 AWS 费用。

# MorrisPhotos.com，第 1 版

MorrisPhotos.com 的第一次迭代(可在 GitHub 上查看[这里](https://github.com/afmorris/MorrisPhotos.com))利用了以下技术:

*   前端技术
    *   [推特自举](http://getbootstrap.com/)
    *   [jQuery](http://jquery.com/)
*   应用技术
    *   [ASP.NET](https://www.asp.net)
    *   [服务堆栈](https://servicestack.net/)
*   数据技术
    *   服务包荷尔蒙
    *   [微软 SQL 服务器](https://www.microsoft.com/en-us/sql-server/sql-server-2016)
*   托管技术
    *   [AWS 弹性豆茎](https://aws.amazon.com/elasticbeanstalk/)
    *   [亚马逊 RDS](https://aws.amazon.com/rds/)
    *   [亚马逊 CloudFront](https://aws.amazon.com/cloudfront/)
    *   [DNSimple](https://dnsimple.com)

许多移动部件，但通常是一个非常简单的网站实现。我会在本地为任何特性/错误开发新代码，然后把它打包到 Elastic Beanstalk。

类似地，我有一个本地 SQL Server 数据库，我将针对它进行开发，然后将任何内容和模式更改传播到 RDS。

## 问题

最终，Elastic Beanstalk+RDS 的托管解决方案意味着我不必关注我的网站的正常运行时间，因为 Amazon 会处理这些。然而，这种不干涉的方法也意味着我要支付更多的费用，大约每月 55 美元。

虽然我可以处理这种程度的费用，但我开始考虑**为什么**我把它放在一个平台上。这种反省让我重新思考我是如何运营我的网站的。

此外，拥有一个拥有动态数据库后端的 web 应用程序的好处是变化是即时的。然而，这排除了需要经常更换的**。MorrisPhotos.com 不是一个需要实时变化的网站。事实上，除了每月更新一次，我很少更新它。**

 **# 向前移动

鉴于上面列出的两个问题，成本和“我不需要实时数据库更新”，我开始将网站转换到以下技术:

*   前端技术
    *   [推特自举](http://getbootstrap.com/)
    *   [jQuery](http://jquery.com/)
*   应用技术
    *   [ASP.NET 核心](https://docs.microsoft.com/en-us/aspnet/core/)
*   数据技术
    *   服务包荷尔蒙
    *   [微软 SQL 服务器](https://www.microsoft.com/en-us/sql-server/sql-server-2016)
*   翻译技术
    *   [HTTrack 网站复印机](http://www.httrack.com/)
*   托管技术
    *   [亚马逊 S3](https://aws.amazon.com/s3/)
    *   [亚马逊 CloudFront](https://aws.amazon.com/cloudfront/)
    *   [DNSimple](https://dnsimple.com)

## 局部发展

考虑到上述不同的技术，开发策略的变化并不大。我仍然在一个 ASP.NET 的 web 应用程序中工作(这一次是 ASP.NET 核心，因为我想从事一些新技术),使用 SQL Server 后端。

不同之处在于，I **没有**将来自 ASP.NET web 应用程序和 SQL Server 后端的更改传播到任何其他地方；这仅仅是为了当地的发展。一旦我完成了网站更新，我只需对我的本地网站运行(非常棒的)HTTrack 网站复制程序。这将生成一个完整的 web 应用程序静态站点，所有链接都保持不变。

由于我已经将照片托管卸载到 Amazon CloudFront，这一代的变化创建了一个非常小的网站(总共 2.5MB)。从这里开始，将生成的 HTML/CSS/JS 上传到前面有 CloudFront 的 S3 桶就变成了一个简单的练习。

最后，我更新了 DNSimple 中的 DNS 记录，现在指向 CloudFront 发行版，而不是弹性豆茎 ELB。

# 好处

这里有两个主要好处:

*   费用
*   速度

我没有最终的成本节约，但我预计我为 MorrisPhotos.com 的账单将少于 1 美元，因为它只是在 S3 托管，前面有 CloudFront 发行版。一旦我有了一个好的月数，我会更新这个页面。

关于速度，这是以对用户的延迟来衡量的，因为不再有任何 speed 处理程序代码执行，也没有任何数据库执行。唯一加载的项目是在用户端，这些仅仅是 HTML、CSS 和 JS 文件。**