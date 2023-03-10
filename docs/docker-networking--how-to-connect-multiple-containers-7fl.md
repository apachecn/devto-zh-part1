# Docker 基础-如何使用 docker 网络连接容器。

> 原文：<https://dev.to/mozartted/docker-networking--how-to-connect-multiple-containers-7fl>

# 简介

因此，docker 是一个非常棒的工具，可以轻松扩展到跨多种设置复制几乎任何环境，关于 docker 及其功能有很多热门词汇，但在本次会议中，我们将回顾使用 docker 构建分散式架构并使用它实现功能。此剧的典型设置是将同一应用程序的两个不同模块分开，以便它们可以单独通信，有趣的是，在 docker 运行该剧的情况下，它们可以使用 docker 网络连接到同一个数据源。

# 先决条件

所以在这篇文章中我们会假设。

*   你有码头工人的经验。即[部署一个简单的容器](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-getting-started)
*   你知道码头工人是什么。

为了清楚起见，我们仍然要定义一些 docker 概念。

## 什么是 Docker

> Docker 是一个专注于构建基于容器的架构的技术，用于改进开发人员的工作流程。
> 
> 容器映像是一个轻量级的、独立的、可执行的软件包，包含了运行它所需的一切:代码、运行时、系统工具、系统库、设置。可用于 Linux 和基于 Windows 的应用程序，容器化的软件将总是运行相同的，不管环境如何

## 设置 Docker 并编写 Docker

Docker 是一个广泛使用的工具，有很多与入门相关的资源，不多费周折，我将重点介绍几个可以帮助你入门的资源。

对于除了 docker 网站上的文档之外的 Linux 开发人员来说，这些资源可以确保基于 Debian 的用户轻松快速地理解要点。

*   对于 Linux 用户(特别是 Debian 的人(ubuntu，Debian，kali 等))[点击这里](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04)
*   对于 Windows 用户，我们知道你们经常使用安装文件，所以 docker 文档提供了很好的杠杆[点击这里](https://docs.docker.com/docker-for-windows/install/)
*   对于 Mac 用户来说，文档也做到了这一点，现在开始[点击这里](https://docs.docker.com/docker-for-mac/install/)

安装 docker 后，您将需要 docker-compose，Docker for Mac 和 Docker for Windows 已经安装了这个，所以您可以开始了，对于房子里的 Linux 用户，我们有工作要做。

1.  运行此命令下载 docker-compose 的最新版本

```
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose' 
```

Enter fullscreen mode Exit fullscreen mode

如果您在安装 curl 时遇到问题[，请点击此处](https://docs.docker.com/compose/install/#alternative-install-options)

1.  对二进制文件应用可执行权限

```
$ sudo chmod +x /usr/local/bin/docker-compose 
```

Enter fullscreen mode Exit fullscreen mode

1.  测试您的安装

```
$ docker-compose --version
$ docker-compose version 1.17.0, build 1719ceb 
```

Enter fullscreen mode Exit fullscreen mode

## 容器的架构

容器并不像听起来那么复杂，事实证明它们是非常简单的概念，它们的架构也是如此，docker 容器只是一个运行在设置上的服务

您的容器在 docker 架构上运行，使用 docker 文件、docker-compose.yml 文件中的配置或 docker run 命令中指定的映像来设置您的容器。如果要相互连接，这些容器通常具有暴露的端口。

你的容器是独立的服务，可以通过网络设置使用对方的资源，这些网络是由 docker-compose 文件创建的

您的 docker 文件设置通常会为您设置一个映像，一个创建容器所基于的概要文件，为了充分解释这一点，我们将对一个节点应用程序进行 docker 化。

## 停靠节点 app

对于这个简单的设置，我们将在一个基于节点的 web 应用程序上设置 docker，以展示 docker 的酷本质。项目的代码可以在参考库中找到。

### 步骤 1 -设置基础应用

因此，首先我们设置了快速应用程序

```
$ npm i -g express-generator #setup the express-generator
$ express # setup an express app
$ touch processes.json 
```

Enter fullscreen mode Exit fullscreen mode

> 运行 pm2 需要 processes.json 设置来帮助管理服务器
> 的进程，通常，我们可以使用
> 来运行应用程序

```
$ node ./bin/www 
```

Enter fullscreen mode Exit fullscreen mode

让应用程序正常运行，但对于 pm2，我们更进了一步，增加了负载平衡，基本上为应用程序的扩展做好了准备。

### 步骤 2 -设置 docker 图像

接下来，我们在基础文件
中设置 docker 映像

```
#Step 1.
FROM node:6.11-wheezy

#Step 2
LABEL version="1.0"
LABEL description="This is our base docker image"
LABEL maintainer "mozart.osita@gmail.com"

#Step 3.
ENV appDir /var/www/app/current

#Step 4.
ENV NODE_ENV production

#Step 5..
# Set the work directory
RUN mkdir -p /var/www/app/current
WORKDIR ${appDir}

#Step 6
ADD package.json ./
RUN yarn install --production

RUN yarn global add forever pm2

ADD . /var/www/app/current

EXPOSE 4500

CMD ["pm2", "start", "processes.json", "--no-daemon"] 
```

Enter fullscreen mode Exit fullscreen mode

在设置中，第一部分指定我们试图从哪些映像构建我们的映像。

```
#Step 1.
FROM node:6.11-wheezy 
```

Enter fullscreen mode Exit fullscreen mode

使用 Label 标签，我们指定关于正在设置的图像的其他信息，

```
#Step 2
LABEL version="1.0"
LABEL description="This is our base Docker image"
LABEL maintainer "mozart.osita@gmail.com" 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们设置环境变量，将我们的环境设置为生产环境，并使用`appDir`变量
在服务器上设置工作目录

```
#Step 3.
ENV appDir /var/www/app/current

#Step 5.
ENV NODE_ENV production

#Step 6..
# Set the work directory
RUN mkdir -p /var/www/app/current
WORKDIR ${appDir} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们在`workdir`中设置`package.json`并运行带有生产标志的 yarn install 命令，我们还将其他目录和文件添加到工作目录中。

```
#Step 7
ADD package.json ./
RUN yarn install --production

RUN yarn global add forever pm2

ADD /var/www/app/current 
```

Enter fullscreen mode Exit fullscreen mode

在所有这些之后，我们暴露了 4500 端口，该端口将用于与外部环境的连接。

```
EXPOSE 4500

CMD ["pm2", "start", "processes.json", "--no-daemon"] 
```

Enter fullscreen mode Exit fullscreen mode

之后的 CMD 命令用 pm2 启动服务器，PM2 是一个基于节点的进程管理器。

### 步骤 3 -构建映像并部署

在此之后，我们建立我们的形象，并设置运行它。

```
$ docker build -t <image name> . #please remember the .(dot) 
```

Enter fullscreen mode Exit fullscreen mode

这将运行一个过程来构建您的图像，之后您可以将您的图像添加到 docker hub，以便能够从任何地方提取它们。
如果您有 docker hub 账户，请继续登录您的终端

```
$ docker login --username=yourhubusername --email=youremail@company.com 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您得到图像 id

```
$ docker images 
```

Enter fullscreen mode Exit fullscreen mode

从列表中，output 获取您的图像的 id 并用存储库名称
标记您的图像

```
$ docker tag bb38976d03cf yourhubusername/reponame:yourtag 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您可以将此推送到 dockerhub

```
docker push yourhubusername/reponame 
```

Enter fullscreen mode Exit fullscreen mode

运行之后，你的容器就变得轻而易举了

```
$ docker run --rm -it -p bindport:exposedport <image-name>:latest 
```

Enter fullscreen mode Exit fullscreen mode

一个容器被发射和设置。

## 连接容器

要将我们的容器与另一个容器连接，我们可以使用 docker-compose 进行设置，有趣的是我们可以运行多个容器和同一应用程序的分散部分。为了实现这一点，我们将建立一个 docker-compose 文件，并从它构建容器，作为一项服务，使用 docker-compose 设置，我们可以将多个容器设置为服务，并通过容器的名称将它们链接起来

这里有一个 docker-compose.yml 文件示例

```
version: '3'

services:
  appplication:
    image: mozartted/base-node:latest
    ports:
      - "4000:4500" 
```

Enter fullscreen mode Exit fullscreen mode

但是我们可以通过 link 标签将我们的容器与另一个容器连接起来，假设我们希望让我们的节点服务与 MongoDB 服务一起运行。

所以我们更新 docker-compose 配置文件。

```
version: '3'

services:
  application:
    image: mozartted/base-node:latest
    ports:
      - "4000:4500"
    links:
      - mongo
  mongo:
      image: mongo:latest
      ports:
        - "27018:27017"
      volumes:
        - ./data:/data/db 
```

Enter fullscreen mode Exit fullscreen mode

使用 links 标记，我们将应用程序容器或服务连接到 mongo 服务，使用 volume 标记，我们在项目文件夹中设置了一个目录数据作为 mongo 容器的数据卷，使用应用程序配置中的链接，我们可以使用 mongo 名称作为服务地址，使用暴露的端口 27017 作为容器中的端口来连接到 mongo 服务。

但是这种连接容器的方法将我们限制在一个项目集合中，因此我们不能跨两个不同的项目连接容器，
使用网络标签，我们可以建立一个跨不同容器和项目库的网络。

```
version: '3'

services:
  appplication:
    image: mozartted/base-node:latest
    ports:
      - "4000:4500"
    links:
      - mongo
    networks: 
      - backend
  mongo:
    image: mongo:latest
    ports:
      - "27018:27017"
    volumes:
      - ./data:/data/db
    networks: 
      - backend
networks:
  backend:
    driver: "bridge" 
```

Enter fullscreen mode Exit fullscreen mode

通过这种设置，容器连接到后端网络，因此，外部容器也可以连接到后端网络，以便能够访问其中的服务。
要获得连接到容器的网络列表，只需运行命令

```
$ docker network ls 
```

Enter fullscreen mode Exit fullscreen mode

使用网络名称，您可以使用命令
将外部内容连接到网络

```
$ docker network connect <network_name> <container_name> 
```

Enter fullscreen mode Exit fullscreen mode

要能够通过访问网络查看容器，只需运行命令

```
$ docker inspect <network_name> 
```

Enter fullscreen mode Exit fullscreen mode

希望跟进此流程，您可以在此处找到此设置的示例回购

## 结论

通过这些，您可以为不同的项目设置容器，并将它们连接起来，使它们使用其他项目中的服务，并且通过更多的配置，可以在部署中实现基于微服务的架构，Docker 是一个非常棒的工具，充分利用它所提供的一切是值得的。