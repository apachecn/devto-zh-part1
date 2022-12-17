# Django 到 Phoenix -第 1 部分:开发用码头

> 原文：<https://dev.to/michaeljones/django-to-phoenix-part-1-56h>

## 背景

我有一个用 Django 建立的网站，我想开始在[凤凰城](http://phoenixframework.org/)写它的一部分。

## 起点

我使用 Docker 部署网站，但是在开发时，我直接运行 Django 的本地服务器。有人建议，在网站中引入一种新的语言&框架的最佳策略是使用 [nginx](https://www.nginx.com/resources/wiki/) 根据 URL 来拆分收到的请求。

这意味着我们可以在本地机器上运行和配置 nginx，或者我们已经在使用 Docker 进行部署，我们也可以开始使用 Docker 容器进行开发，然后将 nginx 层添加到其中。这让人感觉更包容，也不那么混乱。

## 在 Docker 中设置 Django

为了迈出这一步，我们将希望过渡到让当前的 Django 设置在 Docker 中工作。

我认为，理想情况下，我应该能够在开发和生产中使用同一个 order 文件，以使环境尽可能匹配。然而，当我看着我的生产文档时，我不知道如何做到这一点。

所以我们将从一个新的开发 docker 文件开始，我们将其命名为`Dockerfile.dev`。

```
from ubuntu:14.04

run apt-get update -y

# Install dependencies for builds
run apt-get install -y git python python-pip make g++ wget curl libxml2-dev \
        libxslt1-dev libjpeg-dev libenchant-dev libffi-dev supervisor \
        libpq-dev python-dev realpath apt-transport-https

# Install yarn from website
run curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
run echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

run apt-get update -y
run apt-get install -y yarn

# Install node-js via the nave.sh helper script so that we 
# can build our client side applications 
add https://raw.githubusercontent.com/isaacs/nave/master/nave.sh ./nave.sh

run bash nave.sh usemain 6.9.1

# Install python packages
workdir /src
copy ./requirements.txt .
run pip install --upgrade pip
run pip install -r requirements.txt

# Create source code directory
run mkdir dancetimetable

# Copy over various config files & scripts
copy ./dev/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

run mkdir /src/dev

copy ./dev/run-* /src/dev/
copy ./meta.json /src/meta.json

expose 8000

# Run supervisord on launch
cmd /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这是一个相当标准的 Dockerfile 文件。对于那些不熟悉 Docker 的人来说，该文件以`from ubuntu:14.04`开头，上面写着“给我一个空白的 ubuntu 14.04”映像，然后用文件其余部分的命令对其进行更改。[运行命令](https://docs.docker.com/engine/reference/builder/#run)在该映像中运行特定的命令，保存对系统的任何影响。[复制命令](https://docs.docker.com/engine/reference/builder/#copy)允许你将文件从你机器上的工作目录复制到 Docker 镜像中。

有趣的是，当我们复制依赖项和配置文件时，我们没有复制任何网站源代码。这是因为当我们[运行](https://docs.docker.com/engine/reference/commandline/run/)Docker 容器时，我们将文件夹从本地机器“挂载”到容器中。这很重要，这样当我们在机器上编辑&保存一个文件时，Docker 容器中的进程可以立即看到并使用它。如果没有这一点，我们将不得不在每次更改时重新构建 Docker 映像。

*更新 28-10-2017* :有人注意到我现在还在用 Ubuntu 14.04，这个版本已经相当老了。我这样做是因为我的生产环境仍然使用 14.04。我确实计划更新，但目前该版本仍完全维护，我不需要更多的东西。

那么，在运行 Docker 容器时，我们如何挂载这些目录呢？用这样的命令:

```
#!/bin/bash

docker run \
    -p 127.0.0.1:8000:8000 \
    -v `pwd`/logs/gunicorn:/var/log/gunicorn:rw \
    -v `pwd`/logs/nginx:/var/log/nginx:rw \
    -v `pwd`/logs/cron:/var/log/cron:rw \
    -v `pwd`/dancetimetable:/src/dancetimetable:rw \
    -e DANCETIMETABLE_SETTINGS=development \
    --rm \
    --interactive \
    --tty \
    --name dance \
    michaeljones/dancetimetable:dev 
```

Enter fullscreen mode Exit fullscreen mode

重要的一行是`-v` pwd `/dancetimetable:/src/dancetimetable:rw`，这里我们说‘请在运行容器中创建`/src/dancetimetable`目录，以读&写的方式反映本地`dancetimetable`目录的内容’

另一个重要的行是`-p 127.0.0.1:8000:8000`，它表示‘请从容器内部将端口`8000`公开为我们机器上的端口`8000`’这意味着我们可以访问`http://localhost:8000`并查看我们的应用程序。如果我们不告诉 Docker 这样做，我们就无法访问我们的网站。

你可能会注意到这里遗漏了一些细节。例如，我们如何在容器中运行 Django 开发服务器？答案就在文档的最后一行。这指定了启动 Docker 容器时我们想要运行的命令。在我们的例子中是:

```
cmd /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf 
```

Enter fullscreen mode Exit fullscreen mode

这将使用我们的特定配置运行 [supervisord](http://supervisord.org/) 程序。Supervisord 是一个程序，可以被指示启动其他程序并监控它们，确保它们在失败时重新启动。这似乎是在 Docker 容器中运行多个程序的推荐方式，因为 Docker 文件只允许您在最后指定一个 [cmd](https://docs.docker.com/engine/reference/builder/#cmd) 条目。

虽然我们目前并不完全需要 supervisord，因为我们只运行了一个命令，但是我已经从我的生产设置中熟悉了 supervisord，我们很快就会需要运行另一个命令。

那么超级用户的配置是什么样的呢？大概是这样:

```
[supervisord]
nodaemon=true

[program:django]
command=/src/dev/run-django
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0 
```

Enter fullscreen mode Exit fullscreen mode

`supervisord`下的第一个程序块是用于[通用配置](http://supervisord.org/configuration.html#supervisord-section-settings)的，而`nodaemon`选项告诉 supervisord 在前台运行，这样 Docker 容器就不会失去跟踪并过早停止。

第二个块表明我们希望如何运行 Django 开发服务器。命令条目指向一个脚本和它管理的其他设置，以便将 Django 中的所有标准输出和标准错误重定向到容器的标准输出和标准错误，这样我们就可以在控制台中看到它。

那么`run-django`脚本里有什么呢？看起来是这样的:

```
#!/bin/bash

cd /src/dancetimetable

export DOCKER_HOST=`netstat -nr | grep '^0\.0\.0\.0' | awk '{print $2}'`

python manage.py z runserver 0.0.0.0:8000 
```

Enter fullscreen mode Exit fullscreen mode

这将把目录更改为 Django `manage.py`文件所在的位置，并运行常规的`runserver`子命令来运行我们的开发服务器。

两个额外的细节是，首先，我们需要指定`0.0.0.0`作为地址，而不是`localhost`或`127.0.0.1`。我不知道为什么。其次，从 Docker 容器的角度找到我们的主机的地址，并将其作为 DOCKER_HOST。当在数据库设置中指定主机时，我们在 Django 配置文件中选择了这一点。这是因为我没有在 Docker 容器中运行 Postgres 数据库。它运行在容器外的本地机器上，所以 Django 需要知道如何连接到它。这是我找到的做这件事的唯一方法。

## 结论

有了这些，我们可以启动 Docker 容器并访问`https://localhost:8000`来查看我们的网站。现在我们有了运行在 Docker 容器中的 Django，我们有了一个添加 Nginx 的好地方，然后开始引入 Phoenix。

如果您有任何问题或建议，请在下面评论。

*更新 28-10-2017* :我已经编辑了上面的 Dockerfile 代码块，删除了将在下一篇帖子中介绍和讨论的 nginx 元素。