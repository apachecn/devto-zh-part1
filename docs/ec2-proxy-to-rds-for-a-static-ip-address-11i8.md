# 静态 IP 地址的 RDS 的 EC2 代理

> 原文：<https://dev.to/wrschneider/ec2-proxy-to-rds-for-a-static-ip-address-11i8>

*这篇文章最初出现在[我的博客](http://wrschneider.github.io/2017/12/18/rds-ec2-proxy.html)上*

AWS 中的 RDS 实例不获取静态 IP 地址。这通常是好事，不是问题。这为保持可用性提供了灵活性，同时物理 RDS 主机可能会因调整大小或故障转移到不同的可用性区域(AZ)而改变位置。在任一情况下，客户机通过主机名连接到 RDS，AWS 神奇地更新主机名，指向当前活动主机的 IP 地址。

唯一会造成挑战的情况是，当您想从专用/公司网络连接到 RDS，并且必须更新防火墙或 VPN 隧道配置以允许连接到 RDS 时。如果这对你来说不是问题，你可以停止阅读。

问题是防火墙规则、VPN 隧道和 NAT 规则都适用于 IP 地址，而不是主机名。如果您不知道 RDS 实例的 IP 地址，则无法将防火墙配置为取消阻止该实例的流量。

我发现的解决方法是在 RDS 前面放置一个 EC2 服务器作为 TCP 代理。您可以使用`AWS::EC2::Instance`的`PrivateIpAddress`属性给 EC2 实例一个静态 IP 地址，或者使用静态公共路由 IP 的`AWS::EC2::EIPAssociation`资源。然后，您使用 EC2 实例通过主机名将流量转发到 RDS 实例。EC2 实例的 IP 地址随后成为数据库的静态 IP，用于防火墙目的。

有许多不同的方法可以将流量从 EC2 转发到 RDS。你可以挑选最适合你的:

*   `socat`:例如`socat TCP-LISTEN,[port],fork,reuseaddr TCP:[hostname]:[port]`。

    *   优点:简单方便，易于安装`yum install socat`。
    *   缺点:知名度不高；每个连接派生一个进程，因此不适合大容量
*   `haproxy`

    *   优点:强大、可扩展
    *   缺点:AWS Linux 包[不包括运行时 DNS 解析的最新版本](https://stackoverflow.%0Acom/questions/37520737/get-or-install-haproxy-1-6-on-amazon-linux-only-comes-with-1-5-in-epel)；必须从源代码构建，即使这样也需要一些扭曲来在运行时解析主机名。
*   `nginx`

    *   优点:你可能已经在使用它了
    *   缺点:对一个港口运输公司来说太过了
*   `ssh`端口转发

    *   优点:众所周知，ssh/sshd 已经默认安装
    *   缺点:需要建立和认证一个 SSH 连接，当您只需要端口转发时，这是多余的

*关于安全组设置*:将 EC2 实例和 RDS 实例放在两个不同的安全组中，这些安全组可以互相引用。这是 CloudFormation 的 AWS::EC2::SecurityGroupIngress`and AWS::EC2::SecurityGroupEgress`资源的一个完美用例(“通常允许安全组
相互引用”)。因为您不知道 RDS 实例的 IP 地址，所以可以参考 RDS 实例的安全组。EC2 安全组将具有到 RDS 安全组的出口规则，反之亦然。

最好锁定 EC2 安全组。EC2 实例应该只允许对目标 RDS 实例的出站访问，并允许 DNS 进行主机名解析。RDS 实例应该只允许通过 EC2 代理进行入站访问。

*其他 CloudFormation 技巧*:将 EC2 实例单独放在一个堆栈中，这样它就可以被独立地重建。您可以从 EC2 堆栈对 RDS 实例进行跨堆栈引用。此外，您可以在 EC2 的`UserData`属性中放置一个脚本，将 RDS 主机名(来自跨栈引用)注入 Upstart 配置文件(`/etc/init/your-proxy-service.conf`)，这样您的代理服务将在引导时自动启动并引用正确的主机名。