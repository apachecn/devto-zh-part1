# Nginx:反向代理

> 原文：<https://dev.to/just_insane/nginx-reverse-proxy-300p>

在本文中，我们将了解什么是反向代理，以及如何使用 Nginx 在 CentOS 上设置反向代理。

## 什么是反向代理

反向代理是一种代理服务器，它代表客户端从一个或多个服务器检索资源。然后，收集的信息被返回给客户端，就好像它来自 web 服务器本身一样。它与正向代理(允许其客户端联系任何服务器)相反，反向代理允许任何客户端联系其服务器。

## 为什么我需要反向代理

对于许多潜在的用例，您将需要一个反向代理。使用反向代理最常见的原因是为了增加安全性。例如，反向代理可以是您的 DMZ 中的一台加固计算机，它从 internet 获取客户端流量，并将其传递到您的内部 web 应用程序服务器。这将有两个主要的安全好处:

较低的攻击面:您暴露于互联网的每个系统都会增加进入您网络的攻击面。只要其中一个系统受损，就会对您的所有内部系统造成严重破坏。

更容易修补/管理/保护:暴露在互联网上的资源更少也意味着需要修补、管理和保护的关键基础设施更少。当然，您仍然应该对您的内部系统进行修补，但是，最好的安全措施是深度防御，基本上，在攻击能够突破您的内部网络之前，要通过尽可能多的硬层。拥有反向代理会增加进入网络之前的层数。

您希望设置反向代理的另一个原因是，如果您的网络内部有许多资源，如 web 和应用服务器/服务，您希望这些资源都可以在网络外部访问，但您只有一个公共 IP 地址。您可以将所有这些网站放在一个 IP 和端口(80/443)上的反向代理后面，然后让客户端根据它们试图连接的(子)域连接到正确的后端，而不是将网站放在不同的服务上。

反向代理还降低了您的管理成本，例如，您可以获得一个证书(通配符或 SAN)并将其安装在您的反向代理上，而不是获得一个 SSL 证书并将其安装在您拥有的每台 web 服务器上，然后管理所有这些证书。这样，客户机和 web 服务器之间的所有流量都被加密，但是您不必花费额外的时间和金钱来处理在代理后面的每个服务上安装证书。但是请注意，这样做意味着反向代理和 web 服务器(在您的网络内部)之间的流量是不加密的，除非您将内部站点配置为 HTTPS。对于 homelab 环境或测试来说，这通常没什么大不了的，但是请注意，它不太安全，不应该在生产环境中使用。如果您在生产环境中，请确保反向代理和内部应用程序服务器之间的流量是加密的。

## 我应该使用什么反向代理

反向代理有多种类型，从 F5 Big-IPs 等专用企业硬件/软件，一直到 Nginx 或 Apache 等常规 web 服务器。这些都有不同的利弊，你应该自己研究。然而，对于小型博客或 homelab 环境，运行 Nginx 的反向代理就可以了。

## 安装 Nginx 作为反向代理

既然我们已经了解了什么是反向代理，以及为什么我们可能想要使用它，那么让我们看看如何将 Nginx 安装为反向代理。

### 第一步:安装 Nginx

在这一步，我们将看看如何在 CentOS 7 上安装 Nginx。请注意，这些步骤对于您的首选发行版应该是相似的，但是，我不能保证。

首先，更新 CentOS 服务器上的软件包:

```
$ sudo yum -y update 
```

安装 Nginx:

```
$ sudo yum -y install nginx 
```

启动 Nginx 并启用(以便在引导时启动:

```
$ sudo systemctl start nginx
$ sudo systemctl enable nginx 
```

检查 Nginx 是否正在运行:

```
$ sudo systemctl status nginx 
```

一旦 Nginx 运行，我们需要打开防火墙，这样我们的客户就可以访问它。

```
$ sudo firewall-cmd --permanent --add-service=http
$ sudo firewall-cmd --permanent --add-service=https
$ sudo firewall-cmd --reload 
```

现在我们已经完成了，我们应该验证我们实际上能够到达 nginx。在网络浏览器中进入`http://<ip_of_your_host>`，你应该会看到一个 Nginx 页面。如果没有，请确保 Nginx 正在运行，并且您已经打开了防火墙。

### 第二步:配置 Nginx

在这一步中，我们将为 Nginx 安装配置一些合理的默认值，包括 SSL 和代理默认值。

首先，让我们看看如何使用 Nginx 的 snippets 特性来配置 Nginx 中的默认 SSL 和代理设置。

#### 代理默认值

在`/etc/nginx/snippets/`中，创建一个名为`proxy-defaults.conf`的文件，内容为:

```
proxy_redirect off;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $remote_addr; 
```

然后将`include snippets/proxy-defaults.conf`添加到您的服务器块中。

#### SSL 默认值

首先，让我们看看设置 SSL 证书和所需的 dhparam 文件。

##### 获取 SSL 证书

我们将看看设置让我们加密，一个免费的认证机构。

###### 步骤 1 -安装证书机器人让我们加密客户端

启用 EPEL 存储库:

```
$ sudo yum -y install epel-release 
```

安装 cerbot-engine:t0]

```
$ sudo yum -y install certbot-nginx 
```

要使 Cerbot 工作，您需要在`/etc/nginx/nginx.conf`
中指定您的服务器名称

```
$ sudo vi /etc/nginx/nginx.conf 
```

找到已有的`server_name`行:

```
server_name _; 
```

将`_`下划线替换为您的域名:

```
server_name example.com www.example.com; 
```

验证配置文件的语法是否正确:

```
$ sudo nginx -t 
```

如果完成后没有错误，重新加载 Nginx 来加载新的配置:

```
$ sudo systemctl reload nginx 
```

###### 第二步-获取证书

有许多方法可以通过 Certbot 从 Let's Encrypt 获得证书。我们将使用 Nginx 插件来重新配置 Nginx，并在证书更新时重新加载配置。

```
$ sudo certbot --nginx --rsa-key-size 4096 -d example.com -d www.example.com 
```

这将使用`--nginx`插件运行`certbot`，密钥大小为 4096(比默认的 2048 更安全)，并使用`-d`指定我们希望证书有效的域名。

！您需要添加您想要使用的每个子域，让我们加密证书，用`-d`标志分隔。

如果这是您第一次在此服务器上运行 certbot，系统会提示您输入电子邮件地址并同意服务条款。然后，它将进行测试，以确保它可以与 Let's Encrypt 的服务器进行通信。

完成后，系统会询问您希望如何在您的服务器上配置 HTTPS。选择您想要使用的选项，然后按 Enter。然后您将被告知您的证书存储在服务器上的什么位置。

###### 步骤 3 -更新 Diffie-Hellman 参数

目前，如果你使用 [SSL 实验室测试](https://www.ssllabs.com/ssltest/)来测试你的服务器，你会注意到你只能得到 **B** 的成绩。我们可以通过创建一个新的 dhparam.pem 文件并将其添加到我们的`ssl-defaults.conf`文件中来解决这个问题(参见下一节)。

```
$ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 4096 
```

这需要一段时间。记下 dhparam.pem 文件的位置，因为下一步将需要它。

###### 步骤 4 -设置自动续费

咱们的加密证书有效期只有九十天。因此，我们需要设置一个命令来定期运行，该命令将检查即将过期的证书并自动更新它们。

我们将使用`cron`每天运行 certbot 更新命令。为此，我们需要编辑一个名为`crontab`的文件。

```
$ sudo crontab -e 
```

粘贴下面一行，然后保存并关闭文件。

```
15 3 * * * /usr/bin/certbot renew --quiet 
```

让我们打开它，看看这个命令在做什么:

1.  `15 3 * * *` -每天凌晨 3:15 运行以下命令。您可以选择在任何时间运行该命令。
2.  `/usr/bin/certbot` -调用`certbot`实用程序。
3.  `renew` -告诉 Certbot 检查系统上安装的所有证书，并更新任何将在三十天内过期的证书。
4.  `--quiet` -告诉 Certbot 不要输出信息或等待用户输入。

`cron`现在将每天运行此命令。所有已安装的证书在到期前 30 天或更短时间内将自动更新并重新加载。

##### 创建 ssl-defaults.conf 片段

在`/etc/nginx/snippets/`中，创建一个名为`ssl-defaults.conf`的文件，内容为:

```
# These next two lines depend on where certbot placed your SSL certificates
# edit them to match the location of the files
# ssl_certificate /path/to/ssl/certificate
# ssl_certificate_key /path/to/ssl/certificate_key

ssl_dhparam /etc/ssl/certs/dhparam.pem;
ssl_prefer_server_ciphers on;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:50m;
ssl_stapling on;
ssl_stapling_verify on;
# The following setting can be dangerous, please research what it does before uncommenting.
# add_header Strict-Transport-Security max-age=15768000; 
```

然后将`include snippets/ssl-defaults.conf`添加到您的服务器块中。

！！注意:这些默认值可能不适合您的所有需求。您可能需要在服务器块中配置不同的设置。

！另请注意，如果您需要更改这些默认设置，只需从服务器块中移除`include snippets/<item>-defaults.conf`。

这些默认设置允许您从 [SSLLabs](https://www.ssllabs.com/ssltest/index.html) 获得 A+。

#### 默认服务器块模板

应将服务器块添加到`/etc/nginx/sites-available`。该文件应该命名为`<subdomain>.example.com.conf`。

```
server {
    listen 443 ssl;
    server_name www.example.com;
    ssl on;
        # Remember to comment these out if you need to change their defaults
        include snippets/ssl-defaults.conf;
        include snippets/proxy-defaults.conf;

    location / {
        proxy_pass http://<internal_ip>:<port>/;
        # Input any other settings you may need that are not already contained in the default snippets.
    }
} 
```

在创建您的服务器块之后，您需要使用下面的命令将它链接到`/etc/nginx/sites-enabled/`。

```
$ sudo ln -s /etc/nginx/sites-available/www.example.com.conf /etc/nginx/sites-enabled/ 
```

使用以下命令测试 Nginx 配置:

```
$ nginx -t 
```

配置完所有这些设置后，需要重新加载 nginx，这样它就有了新的配置。

```
$ systemctl reload nignx 
```

现在，您应该能够从互联网访问您的内部站点。