# 再也没有理由不使用 SSL 了

> 原文：<https://dev.to/chiangs/theres-no-excuse-not-to-have-ssl-anymore-76f>

> 有了[让我们加密](https://letsencrypt.org/)，为你的网站获得一个免费的 SSL 证书比以往任何时候都容易。在 AWS 上，证书对于您创建的任何负载平衡环境都是免费的。如果您没有运行一个负载平衡的站点，那么就按照这篇文章来做吧。

这些步骤适用于 Amazon Linux 上的 Apache 实例。我们将安装一个 SSL 证书，将其设置为自动重新新建，然后将所有流量自动重定向到 HTTPS。

### 一、我们为什么要这样做？

SSL 或安全套接字层是在 web 服务器和浏览器之间建立加密链接的标准安全技术；它确保在 web 服务器和浏览器之间传递的所有数据保持私密和完整。它验证客户机正在与实际的服务器而不是某个冒名顶替者对话。

不仅如此，拥有 https URL 将有助于您在搜索结果中排名更高，这当然也很好。

为什么不是所有的网站都有 SSL 证书？购买和续订可能会很贵，但有了“让我们加密”，这种情况再也不会发生了。

好吧，我们开始吧。

### 准备

确保您已经打开了实例的端口 443。

登录你的 AWS 面板，进入安全组，找到 HTTPS 和端口 443

设置 Apache 使用 SSL/TLS: Reference

检查 Apache 是否以下列方式启动:`$ sudo service httpd status`

如果需要，启动 Apache: `$ sudo service httpd start`

确保您的所有软件包都是最新的:`$ sudo yum update -y`

现在您的实例是最新的，通过安装 Apache 模块 mod_ssl: `$ sudo yum install -y mod24_ssl`

添加 SSL/TLS 支持，然后重新启动 Apache: `$ sudo service httpd restart`

### 安装 SSL 证书

SSH 到您的服务器

下载 certbot(安装&更新证书所需的加密客户端):

`$ wget https://dl.eff.org/certbot-auto`

`$ chmod a+x certbot-auto`

运行 certbot: `$ sudo ./certbot-auto --debug -v --server https://acme-v01.api.letsencrypt.org/directory certonly -d www.yoursite.domain`

您可以根据需要添加任意多的-d YOUR_SITE_HERE 标志来验证多个域(或子域)。这将启动一个可视化的向导，输入一个管理员电子邮件，然后指向你的 web 根目录(你的 index.html 所在的目录)。在装有 Linux 的 AWS 上，最有可能的是`/var/www/html`除非你按照我以前的文章中关于部署一个完整的 web 应用程序作为整个实例，那么它将是根目录所在的位置。完成向导后，您将拥有有效的 SSL 证书。现在我们只需要将它们添加到 Apache 中。

certbot 会将您的证书放在以下路径:

```
Certificate: /etc/letsencrypt/live/YOUR_WEBSITE_HERE/cert.pem
Private Key: /etc/letsencrypt/live/YOUR_WEBSITE_HERE/privkey.pem
Full Chain: /etc/letsencrypt/live/YOUR_WEBSITE_HERE/fullchain.pem 
```

Enter fullscreen mode Exit fullscreen mode

编辑您的 SSL 配置:`$ sudo nano /etc/httpd/conf.d/ssl.conf`

–将 SSLCertificateFile 设置为您的证书路径(见上文)。

–将 SSLCertificateKeyFile 设置为您的私钥路径(见上文)。

–将 SSLCertificateChainFile 设置为您的完整链路径(见上文)。

保存并退出。

重启 apache: `$ sudo service httpd restart`

此时，您可以测试您的域在 https 上工作。

### 设置 SSL 自动更新

让我们加密证书要求每 90 天更新一次，所以最好配置自动更新。所以我们只需要设置一个简单的 cron 作业。

切换到 sudo 用户:`$ sudo -i`

编辑 crontab，这是所有帐户的 cron 作业列表:`$ crontab -e`

(Crontab 默认使用 vim 编辑，不是 nano。)

按 I 键进入“交互”模式，允许您键入和编辑文件。

添加以下代码行，每天运行两次更新(分别在凌晨 1 点和下午 1 点):

```
0 1,13 * * * /home/ec2-user/certbot-auto renew 
```

Enter fullscreen mode Exit fullscreen mode

按 esc 键退出交互模式。

键入`:wq`写入文件，然后退出 vim。

现在，您的 SSL 证书将自动更新，无需您再次考虑。

如果您的 cron 作业由于某种原因未能成功续订，Let's Encrypt 会方便地向您在向导过程中提供的任何管理员电子邮件地址发送一封电子邮件作为警告。

退出 sudo 用户:`exit`。

### 设置自动重定向到 HTTPS

现在你已经让 HTTPS 工作了，我们应该让它成为别人进入你的 URL 时的默认重定向。

在 Apache 配置文件中编辑您的网站 VirtualHost，并添加以下设置。

首先定位`.conf`文件(类似于/etc/httpd/conf/): `$ sudo find / -name httpd.conf`

转到该文件的根目录:`$ sudo nano httpd.conf`

在文件底部一路添加以下内容:

```
RewriteEngine On
  RewriteCond %{HTTPS} off
  RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} 
```

Enter fullscreen mode Exit fullscreen mode

### 全部搞定！

免费获得自动更新 SSL 有多满意？

问题，评论和反馈总是受欢迎的，尤其是如果你有任何更新方法和建议！