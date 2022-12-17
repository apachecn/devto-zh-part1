# 在 AWS ElasticBeanstalk 上扩展 nginx/Apache 代理配置

> 原文：<https://dev.to/david_ojeda/extend-nginxapache-proxy-configurations-on-aws-elasticbeanstalk-3mjg>

AWS ElasticBeanstalk 应用程序使用 nginx 或 Apache 代理来中继请求。使用。我们可以扩展这些代理的配置。如果你不知道怎么做。ebextensions 工作你可以在这里阅读更多[。](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html)

我将使用. ebextensions 来扩展默认的 nginx 代理配置。

# 创建一个. conf 文件

首先，我们需要创建一个包含所需指令的. conf 文件。nginx 指令列表可以在这里找到。我的 conf 文件——名为 proxy . conf——增加了代理的一些超时时间: