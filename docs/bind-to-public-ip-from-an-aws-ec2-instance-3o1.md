# 从 AWS EC2 实例绑定到公共 IP

> 原文：<https://dev.to/adamkdean/bind-to-public-ip-from-an-aws-ec2-instance-3o1>

我在 Amazon Web Services EC2 实例中遇到的一个问题是绑定到公共 IP 或主机名。当您获得 EC2 实例时，您将可以访问您的本地 IP 和您的本地主机名，但是如果您想要从外部源连接到该实例，您必须绑定到一个公共接口。

可能有一些使用 AWS API 获取实例 id，然后从中获取公共主机名的方法，但是我发现了一种更简单的方法。

绑定到`0.0.0.0`。

[![sudo nc -l 0.0.0.0 80](img/4d0427d7e9ea56fc7f0bd061a0fab14d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bo8Pqmay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/zyYHkpi.png)

如您所见，我现在可以通过外部浏览器访问它。