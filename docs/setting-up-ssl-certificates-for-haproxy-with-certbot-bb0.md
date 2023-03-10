# 使用 certbot 为 HAProxy 设置 SSL 证书

> 原文：<https://dev.to/phansch/setting-up-ssl-certificates-for-haproxy-with-certbot-bb0>

下面介绍如何使用 [certbot](https://certbot.eff.org/) 和[让我们加密](https://letsencrypt.org/)来自动为 [HAProxy](http://www.haproxy.org/) 设置 SSL 证书，而无需重启 HAProxy。

本文假设您已经安装了 certbot 并且已经运行了 HAProxy。

## 肯定 bot 命令

当我们使用 HAProxy 时，我们不能像 nginx 一样运行`sudo certbot --haproxy`,因为 certbot 还没有正式支持 HAProxy。相反，我们必须使用`certonly`命令和`--standalone`选项来运行一个独立的 web 服务器。

我们还想在以后的脚本中包含 certbot 命令，所以我们需要通过命令行提供所有进一步的选项。我们将使用的基本 certbot 命令如下:

```
certbot certonly --standalone --agree-tos --non-interactive \
-m yourmail@host.org -d domain 
```

Enter fullscreen mode Exit fullscreen mode

如果你尝试在运行 HAProxy 的机器上运行这个命令，它会告诉你端口 80 已经被使用了，因为那是 HAProxy 正在监听的端口。为了避免这种情况，我们必须告诉独立服务器使用另一个端口:

```
certbot certonly --standalone --agree-tos --non-interactive \
-m yourmail@host.org -d domain --preferred-challenges http \
--http-01-port 9785 
```

Enter fullscreen mode Exit fullscreen mode

我们还必须告诉 certbot 保存证书，直到它过期，并且当我们向它添加新域时应该更新它

```
certbot certonly --standalone --agree-tos --non-interactive \
-m yourmail@host.org -d domain --preferred-challenges http \
--http-01-port 9785 --renew-with-new-domains \
--keep-until-expiring 
```

Enter fullscreen mode Exit fullscreen mode

certbot 部分不存在，我们可以继续 HAProxy 配置。

## HAProxy 配置

对于 HAProxy，我们首先为我们的示例前端设置一个最小的 SSL 配置:

```
frontend www-https
  bind *:443 ssl crt /etc/haproxy/ssl-certs/cert.pem
  reqadd X-Forwarded-Proto:\ https 
```

Enter fullscreen mode Exit fullscreen mode

我们还会告诉 HAProxy 将所有发送到独立 web 服务器的请求定向到独立 web 服务器的正确端口。

我们的前端现在完成了，看起来像这样:

```
frontend www-https
  bind *:443 ssl crt /etc/haproxy/ssl-certs/cert.pem
  reqadd X-Forwarded-Proto:\ https

  # Let the letsencrypt backend handle requests to the
  # acme-challenge url
  acl letsencrypt-req path_beg /.well-known/acme-challenge/
  use_backend letsencrypt if letsencrypt-req

  # ... etc. 
```

Enter fullscreen mode Exit fullscreen mode

letsencrypt 后端将服务器设置为本地 certbot 独立服务器:

```
backend letsencrypt
   server letsencrypt 127.0.0.1:9785 
```

Enter fullscreen mode Exit fullscreen mode

确保使用`haproxy -c -f /path/to/your/haproxy.cfg`验证配置，以检查错误。

现在我们可以重新加载 HAProxy 配置，并尝试再次从上面运行 certbot 命令。这应该行得通，但我们还没有完成。

## 把所有的东西放在一起

下一步是创建一个脚本，该脚本将执行 certbot 命令，并将生成的证书复制到 HAProxy 正在查找它的目录中。

这个脚本将被称为`cert_renew`，它将接受一个域列表作为参数。

```
#!/bin/bash

# This script takes a list of domains as arguments
# and will setup a single certificate for all of them.

cert_name="generic_cert"
haproxy_cert_dir="/etc/haproxy/ssl-certs"
email="you@host.org"

domains=""
for domain in "$@"
do
  domains+="-d $domain "
done

certbot certonly --standalone --agree-tos --non-interactive \
-m $email --preferred-challenges http \
--http-01-port 9785 --cert-name $cert_name \
--renew-with-new-domains --keep-until-expiring $domains

mkdir -p $haproxy_cert_dir

# Combine the certificate chain and private key and put it
# into the correct HAProxy directory
cd /etc/letsencrypt/live/$cert_name
cat fullchain.pem privkey.pem > "$haproxy_cert_dir/cert.pem"

echo "Reloading haproxy"
sudo systemctl reload haproxy 
```

Enter fullscreen mode Exit fullscreen mode

像`cert_renew domain1.org domain2.net`一样使用它将在`/etc/haproxy/ssl-certs/cert.pem`为两个域设置一个证书，并重新加载 HAProxy。

## 设置 Cronjob

接下来，我们将在`/usr/local/bin/cert_renew`保存脚本并设置 cronjob，以便它每天运行两次:

```
5 7,17 * * * root /bin/bash -c '/usr/local/bin/cert_renew domain1.io 2>&1 | /usr/bin/logger -t certbot' 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们只需在添加新域时更改 cronjob 条目，再也不用担心证书过期了。

## 来源

这篇文章建立在其他几篇非常有帮助的博客文章的基础上。这些是:

*   李·哈钦森的这篇伟大的文章
*   斯科特·赫尔姆的帖子和下面的评论
*   [这个帖子](https://skarlso.github.io/2017/02/15/how-to-https-with-hugo-letsencrypt-haproxy/)也是由 Skarlso