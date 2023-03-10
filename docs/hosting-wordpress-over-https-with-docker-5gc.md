# 用 Docker 托管 HTTPS 的 WordPress

> 原文：<https://dev.to/foresthoffman/hosting-wordpress-over-https-with-docker-5gc>

> 2020 年 10 月 13 日更新:你好！这里是来自未来的森林！我不再使用这种设置。而且，我不打算让它保持最新。因为我不再为自己维护它，而且它是一个非常定制的解决方案，所以我没有时间为它创建一个存储库。
> 
> 该解决方案至少已有 3 年历史。如果你注定要使用 WordPress 的旧版本，而这个解决方案对你有效，我很高兴能帮助你。另一方面，如果你使用的是 WordPress、Nginx、Docker 或 Certbot 的新版本，这里可能会有一些有用的信息，但除此之外，我祝你好运！
> 
> 干杯！😄

[就在几天前](https://twitter.com/ForestJHoffman/status/912031600405123073)，我将我的作品集转移到了一台独立的服务器上，并开始在 HTTPS 提供服务。当一切都完成时，我超级兴奋！我想谈一谈我采取了什么步骤，因为在这个过程中我发现了一些恼人的问题。这不是一个一步一步的教程，而是我在分享最终让它为我工作的配置。

我在这里使用`example.com`作为占位符。如果你正在阅读这篇文章，并试图让你自己的设置工作，你应该用你自己的域替换`example.com`。此外，虽然我怀疑这需要说，不要使用`root`作为您的 MySQL 密码。

## 目录

*   [服务器堆栈](#server-stack)
*   [Docker 编写配置概述](#config-overview)
*   [engine proxy container](#nginx-proxy):[docker-compound . yml](#nginx-proxy-compose)、[docketerfile](#nginx-proxy-dockerfile)、 [crontab](#nginx-proxy-crontab) 、 [startup.sh](#nginx-proxy-startup) 、[nginx . conf【注:engine . conf】](#nginx-proxy-config)
*   [WordPress 容器](#wordpress) : [坞站-化合物. yml](#wordpress-compose)
*   [engine 容器](#wordpress-nginx):[docker-compound . yml](#wordpress-nginx-compose)、[engine . conf](#nginx-config)、[engine/WordPress . conf](#wordpress-nginx-config)
*   [WordPress 容器](#wordpress-wordpress):[dock-compound . yml](#wordpress-wordpress-compose)， [wordpress/wp-config.php](#wordpress-wordpress-wp-config)
*   [Mariadb 容器](#wordpress-mariadb) : [坞站-化合物. yml](#wordpress-mariadb-compose)
*   [用证书机器人安装 SSL 证书](#certbot)
*   [延伸阅读](#further-reading)

## 服务器堆栈

^ [ToC](#toc)

我使用 [Docker](https://www.docker.com/) 来隔离我的服务器，这样它们都可以有自己专用的环境。*如果我在 Docker 容器上搞砸了什么，我可以重建它。另外，我使用 [Docker Compose](https://docs.docker.com/compose/) 来处理我的容器的分组。*

我选择 [Nginx](https://nginx.org/en/) 作为我的代理和我的 WordPress web 服务器。注意，代理在这里不是必需的，但是我在其他一些事情上需要它。我想保持事情的一致性，所以这就是我选择全面使用 Nginx 的原因。

如果你试图实现一个类似的设置，但是没有代理，你也可以为你的 WordPress 容器的 Nginx 服务器提供 SSL 证书，并添加适当的服务器块来支持 SSL。我在下面的 [Nginx 代理容器](#nginx-proxy-config)部分提到了这些。

对于我的 SSL 证书，我使用的是[，让我们通过](https://letsencrypt.org/)[证书机器人](https://github.com/certbot/certbot)来加密。这是一个可爱的小工具，自动化整个证书注册过程。

## Docker 撰写配置概述

^ [ToC](#toc)

以下章节列出了以下容器的所有必要配置:

```
- proxy:
  - nginx
- wordpress:
  - nginx
  - wordpress
  - mariadb 
```

Enter fullscreen mode Exit fullscreen mode

## Nginx 代理容器

^ [ToC](#toc)

**【坞站】-化合物。yml**

```
version: '3.3'

services:
  nginx-proxy:
    container_name: nginx-proxy
    build: .
    image: nginx-proxy:0.0.2
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - "./etc/letsencrypt/:/etc/letsencrypt/"
      - "./etc/ssl/:/etc/ssl/"
    command: bash -c "startup.sh"
    restart: always 
```

Enter fullscreen mode Exit fullscreen mode

容器使用相邻的 docker 文件来构建定制的 nginx-proxy 映像。容器将端口`80`和端口`443`暴露给主机。当容器启动时，它将两个本地目录分别挂载到容器上的`/etc/letsencrypt/`和`/etc/ssl/`目录中。然后，默认启动命令(`nginx -g 'daemon off;'`)被覆盖以运行`startup.sh`文件。最后，通过`restart: always`行，容器被配置为无论何时启动。

```
FROM nginx:1.13.5-alpine

LABEL maintainer="Forest Hoffman<forestjhoffman@gmail.com>"

##
# setting necessary server configurations
##

# add curl and other necessary packages for openssl and certbot
RUN apk add --update \
                curl \
                bash \
                openssl \
                certbot \
                python \
                py-pip \
        && pip install --upgrade pip \
        && pip install 'certbot-nginx' \
        && pip install 'pyopenssl'

RUN addgroup staff
RUN addgroup www-data
RUN adduser -h /home/dev/ -D -g "" -G staff dev
RUN adduser -S -H -g "" -G www-data www-data
RUN echo dev:trolls | chpasswd

##
# copying nginx configuration file
##

COPY nginx.conf /etc/nginx/nginx.conf
RUN chown root:staff /etc/nginx/nginx.conf

# adds certbot cert renewal job to cron
COPY crontab /tmp/crontab-certbot
RUN (crontab -l; cat /tmp/crontab-certbot) | crontab -

# copies over the startup file which handles initializing the nginx
# server and the SSL certification process (if necessary)
COPY startup.sh /bin/startup.sh
RUN chown root:root /bin/startup.sh
RUN chmod ug+rx /bin/startup.sh
RUN chmod go-w /bin/startup.sh 
```

Enter fullscreen mode Exit fullscreen mode

docker 文件将包含在容器中运行 Certbot 的所有必要要求。它还将在容器的 [cron](http://www.unixgeeks.org/security/newbie/unix/cron-1.html) 中添加一行代码，该代码将在清晨以更新模式运行 Certbot(根据 Docker 容器的系统时间)。

**crontab**

```
# Renew Let's Encrypt SSL Certificates that have < 30 days to go,
# in the morning (UTC time).
0 11 * * * /usr/bin/certbot renew --quiet 
```

Enter fullscreen mode Exit fullscreen mode

**startup.sh**

```
#!/bin/bash
#
# startup.sh
#

# Startup the nginx server. The server has to be active for the Let's Encrypt Certbot to
# register and install the certificates.
nginx -g "daemon on;"

# Checks that the SSL certificates are installed. If they are, renews any that are old, and
# installs them if not.
if [[ -d "/etc/letsencrypt/live/example.com" ]]; then certbot renew --quiet
else
        if ! [[ -d "/etc/letsencrypt/live/example.com" ]]; then certbot --nginx -m your-email@ex.com --agree-tos --no-eff-email --redirect --expand -d example.com,www.example.com
        fi
        if ! [[ -f "/etc/ssl/certs/dhparam.pem" ]]; then openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
        fi
fi

# Shuts down the daemonized nginx server and fires up one in the foreground.
nginx -s stop && nginx -g 'daemon off;' 
```

Enter fullscreen mode Exit fullscreen mode

`startup.sh`脚本旨在每当启动容器时运行(即使用`docker-compose up`)。如果认证目录不在容器上的`/etc/letsencrypt/live`目录中，它将只尝试注册认证。`startup.sh`中的路径必须与 Nginx 配置文件中的路径一致。说到这个...

**engine . conf**

```
user www-data;
worker_processes 1;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    # multi_accept on;
}

http {
    #sendfile on;

    upstream docker-wordpress {
            server nginx-wordpress:80;
    }

    # listen for HTTPS requests to example domain
    server {
            ssl_dhparam /etc/ssl/certs/dhparam.pem;
            server_name example.com www.example.com;

            listen 443 ssl; # managed by Certbot
            ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
            ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
            include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot

            location / {
                    proxy_pass       http://docker-wordpress;
                    proxy_redirect   off;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header X-Forwarded-Host $server_name;
                    proxy_set_header X-Forwarded-Proto https;
            }
    }

    # handle ACME challenge from Certbot, and send HTTP requests to HTTPS
    server {
            listen 80;
            server_name example.com www.example.com;

            # listen for ACME challenge from Certbot
            location ^~ /.well-known/acme-challenge/ {
                    # No HTTP authentication
                    allow all;

                    default_type "text/plain";
            }

            location = /.well-known/acme-challenge/ {
                    return 404;
            }

            # Redirect other HTTP traffic to HTTPS
            location / {
                    access_log off;
                    return 301 https://$host$request_uri;
            }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将代理配置为监听到达端口`443`的请求(例如使用`https://*`的请求)。如果请求是针对`example.com`或`www.example.com`域的，那么请求会被传递到`http://docker-wordpress`服务器。`http://docker-wordpress`“上游”服务器被定义为所连接的`nginx-wordpress`容器的端口`80`。使用`docker-compose.yml`文件中的`networks`键将容器连接在一起。

[12/05/17 编辑]:我更新了上面的 Nginx 配置文件，增加了一个服务器块，允许代理通过 Certbot 的 ACME 挑战。这是证书续订所必需的。

任何对端口`80`的请求都没有使用 SSL(只是正常的`http://*`)，所以通过在 url 中强制使用 HTTPS，这些请求被重定向到端口`443`。

这个街区...

```
listen 443 ssl; # managed by Certbot
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot 
```

Enter fullscreen mode Exit fullscreen mode

指示所需域的 SSL 证书将驻留在代理容器上的什么位置。

## WordPress 容器

^ [ToC](#toc)

注意，我在下面的小节中分解了容器配置，但是它们实际上都来自同一个`docker-compose.yml`文件。

这是它看起来的样子。

**【坞站】-化合物。yml**

```
version: '3.3'

services:
  nginx:
    container_name: nginx-wordpress
    image: nginx:latest
    ports:
      - '80'
    volumes:
      - ./nginx:/etc/nginx/conf.d
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./logs/nginx:/var/log/nginx
      - ./wordpress:/var/www/html
    depends_on:
      - wordpress
    restart: always
    networks:
      - nginxproxy_default
  mysql:
    container_name: mysql
    image: mariadb
    ports:
      - '3306'
    volumes:
      - ./mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root
    restart: always
    networks:
      - nginxproxy_default
  wordpress:
    container_name: wp
    image: wordpress-prod:4.8.1-php5.6-fpm
    ports:
      - '9000'
    volumes:
      - ./wordpress:/var/www/html
    environment:
      - WORDPRESS_DB_NAME=wordpress
      - WORDPRESS_TABLE_PREFIX=wpprefix_
      - WORDPRESS_DB_HOST=mysql
      - WORDPRESS_DB_PASSWORD=root
    depends_on:
      - mysql
    restart: always
    networks:
      - nginxproxy_default
networks:
  nginxproxy_default:
    external: true 
```

Enter fullscreen mode Exit fullscreen mode

这里要看到的关键是顶层的`networks`键，它允许每个容器连接到[代理](#nginx-proxy)的默认网络。该网络不是由这个合成文件中的任何容器生成的，所以通过`external: true`线将`nginxproxy_default`网络定义为外部网络。

## Nginx for WordPress 容器

^ [ToC](#toc)

**【坞站】-化合物。yml**

```
###
  nginx:
    container_name: nginx-wordpress
    image: nginx:latest
    ports:
      - '80'
    volumes:
      - ./nginx:/etc/nginx/conf.d
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./logs/nginx:/var/log/nginx
      - ./wordpress:/var/www/html
    depends_on:
      - wordpress
    restart: always
    networks:
      - nginxproxy_default
### 
```

Enter fullscreen mode Exit fullscreen mode

WordPress 容器前面的 nginx 服务器暴露端口`80`，挂载四个目录。前两个用于 nginx 配置，下一个用于保存日志，最后一个用于保存 WordPress 核心文件结构。

**engine . conf**

```
user www-data;
worker_processes auto;

events {
    worker_connections 768;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个基本的配置文件，它包括安装到`/etc/nginx/conf.d`和`/etc/nginx/sites-enabled/`目录的任何附加配置文件。该文件还将`www-data`用户指定为 Nginx 在尝试提供文件服务时应该使用的用户。这意味着服务器的文件结构(安装到`/var/www/html`)应该可以被`www-data`用户/组访问。

**nginx/WordPress . conf**T3 

```
server {
    listen 80;

    root /var/www/html;
    index index.php;

    access_log /var/log/nginx/wp-access.log;
    error_log /var/log/nginx/wp-error.log;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.(png|jpg|jpeg|gif|svg)$ {
        try_files $uri $uri/;
    }

    # Deny access to config.
    location = /wp-config.php {
        deny all;
    }

    # Deny access to htaccess.
    location ~ /\. { 
        deny all;
    }

    # Directly allow access to /wp-admin/admin-ajax.php. This is necessary for
    # WordPress to function on the admin side.
    location ~* ^/wp-admin/admin-ajax.php$ {
        try_files $uri =404;
        fastcgi_intercept_errors on;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Allow access to all other PHP files.
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 Nginx 服务器专门为 WordPress 容器服务。它监听对端口`80`的请求，并将任何对 PHP 文件(包括管理页面)的请求发送到端口`9000`上的 WordPress 容器。这个 nginx 服务器不需要监听端口`443`，因为代理正在处理 SSL 认证。WordPress 服务器上的任何传入的文件请求都将通过代理路由。

## 容器

^ [ToC](#toc)

**【坞站】-化合物。yml**

```
###
  wordpress:
    container_name: wp
    image: wordpress-prod:4.8.1-php5.6-fpm
    ports:
      - '9000'
    volumes:
      - ./wordpress:/var/www/html
    environment:
      - WORDPRESS_DB_NAME=wordpress
      - WORDPRESS_TABLE_PREFIX=wpprefix_
      - WORDPRESS_DB_HOST=mysql
      - WORDPRESS_DB_PASSWORD=root
    depends_on:
      - mysql
    restart: always
    networks:
      - nginxproxy_default
### 
```

Enter fullscreen mode Exit fullscreen mode

WordPress 容器公开了端口`9000`。它有一个用来保存 WordPress 文件结构的卷。它还有四个与连接的数据库相对应的环境变量。注意，`WORDPRESS_DB_HOST`名与连接的 [mysql](#wordpress-mariadb) 容器的`container_name`名相同。

```
<?php
###
// If we're behind a proxy server and using HTTPS, we need to alert Wordpress of that fact
// see also http://codex.wordpress.org/Administration_Over_SSL#Using_a_Reverse_Proxy
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
    $_SERVER['HTTPS'] = 'on';

    // Force SSL in admin.
    define('FORCE_SSL_ADMIN', true);
}

/* That's all, stop editing! Happy blogging. */
### 
```

Enter fullscreen mode Exit fullscreen mode

忽略默认配置，这几行可以放在“停止编辑！”评论。这些行迫使 WordPress 使用 HTTPS 进行内部请求，包括在管理端。

## WordPress 容器的 Mariadb (MySQL)

^ [ToC](#toc)

**【坞站】-化合物。yml**

```
###
  mysql:
    container_name: mysql
    image: mariadb
    ports:
      - '3306'
    volumes:
      - ./mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root
    restart: always
    networks:
      - nginxproxy_default
### 
```

Enter fullscreen mode Exit fullscreen mode

Mariadb/MySQL 容器和其他容器的区别在于被挂载的`mysql`卷和 root-password 环境变量。这个模块中的所有其他配置都与前面的容器非常相似。

## 用 Certbot 安装 SSL 证书

^ [ToC](#toc)

为了在 Nginx 代理上运行 Certbot，服务器必须首先启动并运行。假设认证不在本地的`./etc/letsencrypt`目录中，那么现在尝试运行代理将会失败。我发现为了让代理运行，需要做几件事。

1)需要创建`nginxproxy_default`网络。如果代理以前从未运行过，那么网络就不可能存在。在终端中运行以下命令创建网络:

```
$ docker network create nginxproxy_default 
```

Enter fullscreen mode Exit fullscreen mode

WordPress 容器需要在代理启动之前运行。首先导航到 WordPress 容器的`docker-compose.yml`文件所在的位置。然后在终端中运行以下命令:

```
$ docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

注意:`-d`标志以无头模式启动容器，这意味着您不会看到任何错误，除非您使用命令`docker-compose logs`或通过`docker ps -a`检查容器的状态。

3)在代理容器可以运行 Certbot 之前，需要注释掉证书的路径。在代理的`nginx.conf`中，我必须注释掉包含`listen 443 ssl;`行的服务器块和处理从 HTTP 到 HTTPS 的重定向的服务器块。在他们那里，我暂时进入了这个服务器区块:

```
server {
    listen 80;
    server_name example.com www.example.com;
    location / {
         proxy_pass       http://docker-wordpress;
         proxy_redirect   off;
         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Host $server_name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，可以使用`docker-compose up --build`命令构建 Nginx 代理。Docker-compose 然后将使用非默认的启动命令，该命令将在容器中运行`startup.sh`脚本。我建议在构建代理时不要使用 headless 模式，这样可以确保 Certbot 能够注册证书。

一旦 Certbot 成功创建了证书，就可以删除上面步骤 3 中添加的临时服务器块，并取消对另外两个服务器块的注释。更改配置后，代理需要重新加载一个`docker-compose down && docker-compose up -d`。

用`docker ps -a`做一个快速检查应该会显示如下内容:

```
CONTAINER ID  IMAGE                            COMMAND                 CREATED     STATUS        PORTS                                     NAMES
62b3a673f1cb  nginx-proxy:0.0.2                "nginx -g 'daemon ..."  3 days ago  Up 2 minutes  0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp  nginx-proxy
ea4acq9cc868  nginx:latest                     "nginx -g 'daemon ..."  4 days ago  Up 3 minutes  0.0.0.0:8081->80/tcp                      nginx-wordpress
c7badc76c834  wordpress-prod:4.8.1-php5.6-fpm  "docker-entrypoint..."  4 days ago  Up 3 minutes  0.0.0.0:8082->9000/tcp                    wp
2ba321f4afdd  mariadb                          "docker-entrypoint..."  4 days ago  Up 3 minutes  0.0.0.0:8083->3306/tcp                    mysql 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，导航到`https://example.com/wp-admin`应该会显示良好的 ole WordPress 设置屏幕。

## 进一步阅读

^ [ToC](#toc)

*   [坞站复合文档](https://docs.docker.com/compose/compose-file/)
*   [用 WordPress 快速启动 Docker](https://docs.docker.com/compose/wordpress/)
*   坞站上的 nginx 图像
*   [在 Nginx 上启用 SSL](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)
*   [将 Nginx 作为代理](https://www.digitalocean.com/community/tutorials/docker-explained-how-to-containerize-and-use-nginx-as-a-proxy)****