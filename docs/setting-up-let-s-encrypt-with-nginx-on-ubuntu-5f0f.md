# 在 Ubuntu 上设置让我们用 NGINX 加密

> 原文：<https://dev.to/stayallive/setting-up-let-s-encrypt-with-nginx-on-ubuntu-5f0f>

这篇指南更多的是参考我自己如何在 Ubuntu(任何受 [certbot](https://github.com/certbot/certbot) 支持的 Linux 发行版都可以)上设置一个新的自动更新证书。

_ **注意:**所有命令都应该以根用户身份运行，所以在启动之前切换到根用户(根据您的设置，通过运行`sudo su`或`su -`)。_

#### 0。设置相关性

我们需要`git`从 GitHub 下载 [certbot](https://github.com/certbot/certbot) 。

```
# Update the package repositories to install the latest
apt-get update

# Install git
apt-get install git 
```

Enter fullscreen mode Exit fullscreen mode

#### 1。设置让我们加密证书机器人

```
# Switch to a working directory
cd /opt

# Clone the certbot repository into the certbot folder
git clone https://github.com/certbot/certbot

# Create a directory to hold our configuration file(s)
mkdir /etc/certbot

# Create a directory to hold certbot validation files
mkdir -p /var/www/letsencrypt 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。设置证书机器人配置文件

```
# Start a new file in the configuration directory we just created
nano /etc/certbot/domain.com.conf 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到该文件:

```
# Use the webroot authenticator. 
authenticator = webroot

# Use the following path for the webroot authenticator to use
webroot-path = /var/www/letsencrypt

# Generate certificates for the specified domains, add multiple domains by seperating them with a comma
domains = domain.com, www.domain.com

# Register certs with the following email address
email = your@email.com

# Use a 4096 bit RSA key instead of 2048
rsa-key-size = 4096 
```

Enter fullscreen mode Exit fullscreen mode

用您自己的域名和电子邮件地址替换域名和电子邮件地址:)

#### 4。编辑您的域 NGINX 配置

打开您的 nginx vhost 配置文件，添加以下位置块:

```
server {
    listen 80;

    server_name domain.com;

    location ^~ /.well-known {
        allow all;
        alias /var/www/letsencrypt/.well-known;
    }

    # ... snip ... #
} 
```

Enter fullscreen mode Exit fullscreen mode

在 http 服务器块中添加位置块，或者如果您已经有了有效的证书，也可以将它放在 https 服务器块中。

通过重启 NGINX: `service nginx restart`应用更改(当然是在您通过运行`service nginx configtest`检查您的配置是否有效之后)。

##### 5。申请证书

执行以下命令，并按照屏幕上的指示签发证书。

```
/opt/certbot/letsencrypt-auto certonly -c /etc/certbot/domain.com.conf 
```

Enter fullscreen mode Exit fullscreen mode

#### 6。配置 NGINX 以使用证书

将 vhost 配置更改如下:

```
# Redirect to HTTPS
server {
    listen 80;

    server_name domain.com

    location ^~ /.well-known {
        allow all;
        alias /var/www/letsencrypt/.well-known;
    }

    return 301 https://domain.com$request_uri;
}

# HTTPS server block
server {
    listen 443 ssl;

    server_name domain.com;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/domain.com/privkey.pem;

    location ^~ /.well-known {
        allow all;
        alias /var/www/letsencrypt/.well-known;
    }

    # ... snip ... #
} 
```

Enter fullscreen mode Exit fullscreen mode

通过重启 NGINX: `service nginx restart`应用更改(当然是在您通过运行`service nginx configtest`检查您的配置是否有效之后)。

#### 7。检查一切是否正常

访问您的域名，查看浏览器是否添加了绿色挂锁，并通过 [SSL 实验室](https://www.ssllabs.com/ssltest/)运行您的网站，以验证所有内容是否正确和安全。

**注意:** *我关注的是使证书工作并启用 HTTPS 的最小变化。然而，有许多设置和考虑因素可以使它真正安全，并在 [SSL 实验室](https://www.ssllabs.com/ssltest/)获得 A+评级。更多信息请咨询:*[https://raymii.org/strong-ssl-security-on-nginx](https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html)

#### 8。自动续订证书

为此，我们将使用一个 cron 脚本，该脚本每月运行一次，更新我们的证书并重启 NGINX。

```
# Create a new monthly cron file
nano /etc/cron.monthly/renew-ssl-certificates 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到该文件:

```
#!/bin/bash

/opt/certbot/letsencrypt-auto certonly -c /etc/certbot/domain.com.conf --renew-by-default

service nginx restart 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记将`/etc/certbot/domain.com.conf`改为您自己的配置文件名称。

最后一步是使 renew cron 成为可执行文件，对于该运行:

```
chmod +x /etc/cron.monthly/renew-ssl-certificates 
```

Enter fullscreen mode Exit fullscreen mode

#### 9。利润！

您现在有了一个免费的*证书，让我们加密证书并运行它，无需动一根手指就能自动更新:)太棒了！*