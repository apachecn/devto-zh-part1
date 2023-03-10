# 以编程方式创建 DNS 记录

> 原文：<https://dev.to/funkysi1701/creating-dns-records-programmatically-26lp>

DNS 是互联网的主干。它将域名转换成 IP 地址。但是我们如何用它做聪明的事情呢？

更改 DNS 一直是一件痛苦的事情。你必须记住你注册域名的公司，然后找到他们网站的登录信息，登录并填写一些网页表格，然后等待这些变化在互联网上复制。

有各种各样的网站会给你指定一个自定义的域名，比如 name.domain.com，显然他们必须通过编程来实现，他们没有人按照上面的流程来做。

让我们看看如何做到这一点。

我首先想到的是利用 DNSimple API。https://developer.dnsimple.com/v2/该 API 允许所有类型的 DNS 更改，但这是一项付费服务，所以让我们看看其他选项。

Azure 现在有一个 DNS 部分。您可以转到门户网站，添加一个 DNS 区域，然后根据需要添加任意多的 A、CNAME 和 MX 记录。这当然不比手动做好多少。

Azure 提供了一个 [SDK](https://docs.microsoft.com/en-us/azure/dns/dns-sdk) 和一个示例 Visual Studio 项目。让我们看看它是如何工作的。

首先，您需要设置一些凭据来使用您的 Azure 订阅。最简单的方法是使用 powershell。

。要点表{ margin-bottom:0；}

这将创建一个可以访问您的 Azure 订阅的广告服务主体。

您需要从 Azure 门户获取以下 id。

您的订阅的 subscription ID

tenantId 或 Azure AD 目录 ID

上面创建的服务主体的 application ID

您在上面的 powershell 脚本中输入的密码

。要点表{ margin-bottom:0；}

上面的代码连接到 Azure 并为 test.funkysi1701.com 创建一个指向 ip 地址 1.2.3.4 的 A 记录。

可以用类似的方式创建其他 DNS 记录。

上面的例子创建了一个新的记录集，但是你可以使用下面的例子来删除或获取现有的记录。

。要点表{ margin-bottom:0；}

现在，我发现我可以编写一个控制台应用程序来编辑我的 DNS 记录，我需要更改我所有域名的名称服务器，以便我可以利用这一新功能。

帖子[以编程方式创建 DNS 记录](https://www.funkysi1701.com/2017/10/16/creating-dns-records-programmatically/)最早出现在 [Funky Si 的技术讲座](https://www.funkysi1701.com)上。