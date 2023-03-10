# 使用 Docker Compose Entrypoint 检查 Postgres 是否正在运行

> 原文：<https://dev.to/kellyjandrews/docker-compose-entrypoint-to-check-for-postgres>

## 认识码头工人

在过去的几周里，我一直在努力更深入地了解 Docker。即使现在断断续续用了一年左右，我仍然觉得我还有很多东西要学。这是一个不断变化的景观，但在更深的层次上学习真的很有趣。

我一直在努力理解如何启动和应用程序，并确保 [Postgres](https://www.postgresql.org/) 正在运行。我在为[代码船](http://codeship.com/)做一些新教程的时候遇到了这个问题。让我们一起来看看这个问题，以及它是如何表现出来的。

## 问题

Docker 有两种方法可以让你的容器按顺序启动，其中之一就是`depends_on`。通常情况下——这是一个很好的解决方案，但是它并没有真正告诉您服务是否正在运行，只是告诉您容器已经启动。

Docker 是这样解释的:

> 例如，等待数据库准备就绪的问题实际上只是一个更大的分布式系统问题的子集。在生产中，您的数据库随时可能变得不可用或移动主机。您的应用程序需要对这些类型的故障具有弹性。

我的`Dockerfile`也相当基础-

```
FROM node:7.7.2-alpine

COPY package.json /tmp/package.json
RUN cd /tmp && npm install --quiet
RUN mkdir -p /usr/app && cp -a /tmp/node_modules /usr/app

WORKDIR /usr/app
COPY ./ /usr/app/ 
```

Enter fullscreen mode Exit fullscreen mode

没有什么太疯狂的——只是安装 npm 模块，复制应用程序文件，然后我让 compose 运行命令来启动应用程序本身。为了启动我的应用程序，Postgres 必须运行一个快速检查，以验证桌子已经设置好并准备好滚动。

我遇到麻烦的地方是，我偶尔会看到,`pg`模块无法连接到客户端，通常是在重建映像时，尤其是在测试期间。我会注意到，Postgres 容器仍在准备接受连接，而 node 应用程序已经在尝试开始测试——这导致了一种竞争情况。

值得注意的是，使用 Codeship 将应用部署到 [Heroku](https://www.heroku.com/) ，`depends_on`对我来说不是一个选项，所以我使用`links`来连接一切。因为我们也不能真正保证`depends_on`的任何东西，我对此没有意见，它也没有造成问题，但是测试*必须工作*否则我将永远无法运行部署。

## 解

让我先说几件事:

1.  Docker 可以通过多种方式解决这个问题。我不知道他们所有人，但这是一个可行的解决方案。
2.  我一直在寻找新的方法，所以欢迎建设性的意见。
3.  Docker 总是在变化，所以如果你在最初发布日期之后阅读这篇文章，它可能已经过时了。

### 输入类型

我不打算在这里对 [`entrypoint`](https://docs.docker.com/engine/reference/builder/#entrypoint) 进行详尽的回顾，但是我到目前为止所能学到的是，与`CMD`结合，它本质上将作为一个包装器脚本工作，可以在某些事情完成后运行一个脚本。

这里的想法是，我可以等待`psql`响应，然后我可以启动我的应用程序。我仍在处理一些细节，但脚本看起来是这样的:

```
#!/bin/sh
# wait-for-postgres.sh

set -e

cmd="$@"

while ! pg_isready -h "postgres" -p "5432" > /dev/null 2> /dev/null; do echo "Connecting to postgres Failed"
   sleep 1
 done

>&2 echo "Postgres is up - executing command"
exec $cmd 
```

Enter fullscreen mode Exit fullscreen mode

在文档和堆栈溢出之间，我能够拼凑一个 shell 脚本来使用`pg_isready`命令确定`postgres`容器是否接受连接。一旦成功，我就可以传递任何将要执行的命令，并变成`PID 1`，因为我正在使用`exec $cmd`来运行`npm test`。

### 修改

我确实需要修改一些东西。我最初使用的是使用[忙盒](https://busybox.net/about.html)的`alpine`图像，这是一个超级轻量级的 Linux 版本。我遇到的困难是我需要安装 postgres 工具来使`pg_isready`函数可用。我切换到`slim`图像，并修改了一下我的`Dockerfile`。

```
FROM node:7.7.2-slim

RUN apt-get update && apt-get install -f -y postgresql-client
COPY package.json /tmp/package.json
RUN cd /tmp && npm install --quiet
RUN mkdir -p /usr/app && cp -a /tmp/node_modules /usr/app

WORKDIR /usr/app
COPY ./ /usr/app/
ENTRYPOINT ["./wait-for-postgres.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

三个主要变化:

1.  切换到`node:7.7.2-slim`图像以获得更多可用的 Linux 工具。
2.  运行`RUN apt-get update && apt-get install -f -y postgresql-client`来安装`wait-for-postgres.sh`脚本工作所需的工具。
3.  运行 shell 脚本，然后一旦脚本中的循环退出，我传递给它的任何命令都会运行。

现在——我有能力运行`npm run dev`或`npm test`,并且知道 postgres 已经准备好了。

## 总结起来

我还在做一些事情，但是想先分享一下。例如，在执行应用程序之前测试表是否也已设置好，在节点应用程序本身中探索更好的重试逻辑，等等，但这是可行的。

了解 Docker 的一切以及如何在 Codeship 上交付令人兴奋。像任何事情一样，可以说有起有落。有时调试可能会很乏味，但这对于学习来说是很小的代价。

期待您的评论——在这里或 Twitter 上的 [@kellyjandrews](https://twitter.com/kellyjandrews) 。