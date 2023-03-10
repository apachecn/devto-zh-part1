# Django 到 Phoenix -第 2 部分:Nginx

> 原文：<https://dev.to/michaeljones/django-to-phoenix---part-2-nginx-9kg>

## 背景

我有一个用 Django 建立的网站，我想开始在[凤凰城](http://phoenixframework.org/)写它的一部分。

*   [第 0 部分:背景](https://dev.to/michaeljones/django-to-phoenix-part-0-6ij)
*   [第 1 部分:开发对接器](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)

## 起点

在最后一节中，我们设置了一个开发 Dockerfile & image，这样我们就可以从一个 [Docker](https://www.docker.com/) 容器中运行 Django [runserver](https://docs.djangoproject.com/en/1.11/ref/django-admin/#runserver) 。我们这样做是为了将 [nginx](https://www.nginx.com/resources/wiki/) 添加到容器中，以便在 Django 开发服务器和未来的 Phoenix 开发服务器之间分割请求。

## 将 Nginx 添加到 docker 文件中

在上一篇文章中，我们介绍了 Dockerfile `copy`命令，它允许我们将文件从工作目录复制到 Docker 镜像中。因此，我们将使用它来为我们的映像添加 nginx 配置。这有 4 个步骤。

### 第一步:用 apt-get 安装 nginx

在 Dockerfile 的顶部附近，我们有一个运行 [apt-get](https://en.wikipedia.org/wiki/APT_(Debian)) 的`run`命令来安装我们需要的各种 Ubuntu 包。我们需要更新行来添加' nginx '包。所以它看起来会像:

```
# Install dependencies for builds
run apt-get install -y git python python-pip make g++ wget curl libxml2-dev \
        libxslt1-dev libjpeg-dev libenchant-dev libffi-dev supervisor \
        libpq-dev python-dev realpath apt-transport-https nginx 
```

Enter fullscreen mode Exit fullscreen mode

在安装线的末端有`nginx`。请注意，像这样更改这样一个早期且重要的行需要对 Docker 映像进行相当长时间的重建，因此请记住这一点。

### 第二步:添加`nginx.conf`

这是 nginx 的基本配置。像 Apache 一样，nginx 有一个基本配置和站点特定配置文件的模式。为了完整起见，我将在这里包括基本配置，但我很确定我没有对标准 nginx 配置做任何更改(尽管为了简洁起见，我去掉了注释):

```
user www-data;
worker_processes 4;
pid /run/nginx.pid;

daemon off;

events {
        worker_connections 768;
}

http {

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        gzip on;
        gzip_disable "msie6";

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们用这样的一行代码将它添加到 Dockefile 文件中:

```
copy ./dev/nginx.conf /etc/nginx/ 
```

Enter fullscreen mode Exit fullscreen mode

当我们在 Docker 中构建 Ubuntu 映像时，我们将配置复制到 Ubuntu 希望看到 nginx 配置的标准位置。我已经将这一行添加到非常靠近 docker 文件底部的地方，但是它放在哪里并不重要，只要它在安装 nginx 的 apt-get 命令之后。

**注意**:当我写这篇文章的时候，我意识到这个配置可能和 nginx 安装在容器中的配置一样，所以这可能是多余的。耶，为了写作！

### 第三步:添加我们的站点配置

在上面配置的底部，你可以看到一行:`include /etc/nginx/sites-enabled/*;`。这意味着当 nginx 启动时，它将读取系统目录中的任何附加配置文件。因此，我们将站点配置添加到目录:

```
copy ./dev/site.conf /etc/nginx/sites-enabled/ 
```

Enter fullscreen mode Exit fullscreen mode

其中我们的`site.conf`看起来是这样的:

```
 server {
    listen 8000;

    server_name localhost;

    location / {

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_set_header Host $http_host;

        # we don't want nginx trying to do something clever with
        # redirects, we set the Host: header above already.
        proxy_redirect off;

        proxy_pass http://localhost:7000;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个重要的部分是:`listen 8000`，它告诉 nginx 在那个端口上监听传入的请求。注意，那是我们运行 Django runserver 的端口，所以我们要把它移到另一个端口，因为你不能让两个程序监听同一个端口。

接下来重要的部分是:`location /`。这将创建一个仅适用于与指定位置匹配的请求的配置块。匹配的完成使得对任何以`/`开始的路径的请求都匹配。由于所有的路径都以`/`开头，这将匹配所有的路径。

注意，当我说“路径”时，我的意思是主机之后的 URL 的一部分。所以`https://www.tangotimetable.com/events`的路径将是`/events`。由于这是从`/`开始的，它将匹配我们的位置块。

在定位块内部，最重要的部分是`proxy_pass http://localhost:7000;`。这告诉 nginx 将所有请求转发给监听端口`7000`的进程。一旦我们将 Django runserver 更改为侦听该端口，它将成为我们的 Django run server。

该配置基于我的生产配置，其中需要其他`proxy_`选项。老实说，我已经忘记他们是做什么的了。

### 第四步:删除默认站点配置

当你在 Ubuntu 系统上安装 nginx 时，它带有一个默认的站点配置，监听端口 80 并显示 nginx 登录页面。对于我们的开发设置来说，这不是问题，因为我们没有使用端口 80，但在生产中，当我们可能需要使用时，这是一个问题。这也是吸引我的地方，所以我想强调一下。为了消除它，我们在 docker 文件中添加了下面几行:

```
# Remove default nginx config which attempts to bind to port 80
run rm /etc/nginx/sites-enabled/default 
```

Enter fullscreen mode Exit fullscreen mode

## 更改 Django runserver 端口

如上所述，我们正在从监听端口`8000`的 Django runserver 切换到监听端口`7000`。这样我们就可以让 nginx 监听端口`8000`。所以我们需要更新启动服务器的`run-django`脚本。这个剧本在上一个帖子中介绍过[。](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)

这里我们把它改成这样:

```
- python manage.py z runserver 0.0.0.0:8000 + python manage.py z runserver 0.0.0.0:7000 
```

Enter fullscreen mode Exit fullscreen mode

## 在容器中启动 nginx

最后，我们需要确保我们的 nginx 进程在容器启动时启动。如果您还记得，我们使用一个名为 [supervisord](http://supervisord.org/) 的程序来管理在容器中运行的进程，因此我们必须为 nginx 添加一个配置条目到我们的 supervisord 配置中。

这看起来像:

```
[program:nginx]
command=/usr/sbin/nginx
stdout_events_enabled=true
stderr_events_enabled=true 
```

Enter fullscreen mode Exit fullscreen mode

我把它放在了`[program:django]`块的上方，但我怀疑这有什么关系。

## 结论

就是这样！如果我运行上一篇文章中的`docker run`脚本，我会看到这样的输出:

```
2017-10-28 09:18:42,008 CRIT Supervisor running as root (no user in config file)
2017-10-28 09:18:42,012 INFO supervisord started with pid 7
2017-10-28 09:18:43,015 INFO spawned: 'nginx' with pid 10
2017-10-28 09:18:43,017 INFO spawned: 'django' with pid 11 2017-10-28 09:18:44,034 INFO success: nginx entered RUNNING state, process has stayed up for >  than 1 seconds (startsecs)
2017-10-28 09:18:44,034 INFO success: django entered RUNNING state, process has stayed up for >  than 1 seconds (startsecs) 
```

Enter fullscreen mode Exit fullscreen mode

它显示 supervisord 启动了 nginx 和 Django，并且很高兴它们保持运行超过 1 秒钟。如果我访问`http://localhost:8000`，我会看到我的 Django 应用主页，正如我所料。

如果您有任何问题或建议，请在下面评论。