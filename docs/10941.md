# 如何使用 LetsEncrypt 和 MUP 为您的流星站点获得免费 SSL 证书

> 原文：<https://dev.to/diskdrive/how-to-get-free-ssl-certificate-for-your-meteor-site-using-letsencrypt-and-mup-4i14>

最近，我们使用 [LetsEncrypt](https://letsencrypt.org/) 将 HTTPS 添加到了[webstandup.com](http://webstandup.com/)中。这是一个步骤列表，记录了我需要做的事情和我遇到的问题。

请注意，LetsEncrypt 仍处于测试阶段，因此这些说明在阅读时可能已经过时。然而，这个过程的基本概念是

1.  你在你的服务器上安装 LetsEncrypt 的 [CertBot](https://certbot.eff.org/) 。这基本上是一个程序，它通过监听计算机上的一个端口(通常是端口 80)来验证你拥有你想要证书的域名，然后调用这个域名。
2.  一旦它验证了域名，就会为它生成一个证书
3.  您将这个证书复制到您的开发机器上，然后在您的 [MUP](https://github.com/arunoda/meteor-up) 配置中引用它
4.  您运行 mup 设置和 mup 部署

### 安装证书机器人

这是一个关于如何为你的网络服务器和操作系统安装 CertBot([https://certbot.eff.org/#ubuntutrusty-other](https://certbot.eff.org/#ubuntutrusty-other))的好指南。

对于我来说，要安装 CertBot，我在服务器上运行这些命令

> wget[https://dl . eff . org/cerbot-auto](https://dl.eff.org/certbot-auto)
> 
> chmod a+x cerbot-auto

### 运行 CertBot

安装后，您可以通过运行以下命令来启动 CertBot

> 。/certbot-auto certonly

CertBot 现在应该可以加载了，并且应该给你两个选项 webroot 或者 standalone。

我选择了独立。实际上，如果我使用 webroot 版本会更好(我以后可能还会尝试)，但是我现在还不能让它与 MUP 一起工作。不同的是，standalone 使用端口 80 或 443 创建自己的服务器来进行验证，这意味着当您想要运行 CertBot 来更新您的证书(每 3 个月)时，您基本上需要停止您的应用程序一小段时间。Webroot 使用现有的 web 服务器，这意味着没有停机时间。

无论如何，选择独立后，你会被告知输入你的域名。我输入我的地址的 www 版本(例如[www.webstandup.com](http://www.webstandup.com))，然后使用 GoDaddy 的转发来重定向所有根域(例如 webstandup.com)的流量。参见此[链接](https://www.godaddy.com/help/manually-forwarding-or-masking-your-domain-name-422)获取说明。

另外，在这一点上，您可能应该确保您的端口 80 和 443 端点是开放的。

如果成功，将在/etc/letsencrypt 位置成功创建证书

### 获取本地机器的证书

首先，为了安全起见，您可能需要备份 letsencrypt 文件夹。为了做到这一点

> cd /etc
> 
> 将输出文件夹打包成 tar-cvvf lets encrypt _ yyyymmdd . tar lets encrypt
> 
> 在你的本地机器上，运行下面的
> 
> scp[root @ xx . xx . xx . xx](mailto:root@xx.xx.xx.xx):/etc/lets encrypt _ yyymmdd . tar lets encrypt _ yyymmdd . tar

接下来，您希望转到应该位于/etc/lets encrypt/live/[domain name]的 cert 文件夹，并通过运行以下命令将证书链连接在一起

> cat fullchain.pem privkey.pem > ssl.pem

将 ssl.pem 复制到您的本地计算机，最好是在您的 mup.json 文件旁边——您将会引用它。

在 mup.json 文件中引用证书

您需要对您的 mup 文件进行以下更改

插入以下内容

> "ssl": {
> 
> "pem": "./ssl.pem"
> 
> },

您的 ROOT_URL 现在应该以 https 开头。

我不得不把我的端口改成 80。我不确定这是为什么。以前我让 Webstandup 在 3000 端口上运行。我刚刚将我的公共端口 80 流量路由到专用端口 3000，看起来没问题，但 MUP 似乎不喜欢 SSL。

总之，在您完成这些更改之后，您应该能够运行 mup 安装和 mup 部署了。

### ~~下次运行 CertBot 时关闭站点~~

所以让加密证书在三个月后过期，这样至少你需要每三个月获取一个新证书。我实际上还没有运行更新过程，一旦我这样做了，我会在这里记录它，但这是独立插件的缺点，因为每三个月，你基本上需要关闭你的应用程序，以便你可以运行你的 CertBot 服务器。

~~要用你的流星应用程序做到这一点，你需要关闭你的流星应用程序，它将监听端口 80，你需要关闭[螺柱](https://github.com/bumptech/stud)——这是 MUP 的 SSL 终端，将监听端口 443。~~

~~要关闭应用程序，只需在本地机器的 mup 文件夹中运行此~~

> ~~mup 停止~~

~~要关闭 Stud，在您的服务器上运行下面的~~

> ~~sudo 停止螺柱~~

完成 CertBot 后，记得运行 mup 设置& & mup 部署。

**更新证书**

为了更新证书，只需要做以下步骤

1.  加载到服务器
2.  快跑。/cert bot-自动续订
3.  运行 sudo 服务 nginx 重新启动
4.  (可选)要检查证书是否有效，可以随时访问网站并检查证书的到期日期