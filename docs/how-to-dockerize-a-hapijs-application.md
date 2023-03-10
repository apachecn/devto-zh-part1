# 如何对 hapijs 应用程序进行归档

> 原文：<https://dev.to/jjude/how-to-dockerize-a-hapijs-application>

Docker 极大地增强了我们开发和部署 web 应用程序的方式。使用 docker，我们可以隔离我们的开发环境，并尽可能地模拟生产环境。当我们的开发环境类似于生产环境时，我们可以放心地进行部署。

我们甚至可以更进一步。通过自动化测试和一些其他工具，我们可以自动化部署。这给我们、我们的团队和我们的客户带来了轻松和自信。**在软件开发中，轻松和自信就是价值**。

在本教程中，我将向您展示如何对现有的 hapijs 应用程序进行 dockerize。

Hapijs 是来自沃尔玛的 nodejs 框架。他们用它来驱动他们的电子商务系统。如果你需要关于 hapijs 的介绍，你可以阅读我的[关于 hapijs 的介绍帖](https://jjude.com/hapi-with-tsc/)。

首先，我们将创建一个简单的 hapijs 应用程序。为此，让我们创建`package.json`文件。

```
{
  "name": "sample-hapijs-inside-docker",
  "version": "1.0.0",
  "description": "Hapijs app in docker container",
  "main": "index.js",
  "scripts": {
      "start": "node src/server.js",
      "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Joseph Jude",
  "license": "ISC",
  "dependencies": {
    "hapi": "14.0.0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个常见的`package.json`文件。我想让你们注意这里的两行。

我们正在使用一个启动脚本:`node src/server.js`。随着我们的进展，事情会变得清楚的。请记住这一点。

另外，请注意我们使用的是 hapijs 版本 14。

现在让我们创建`server.js`文件。

```
const Hapi = require('hapi');

const server = new Hapi.Server();
server.connection({ port: 8080 });

server.route({
    method: 'GET',
    path: '/',
    handler: function (request, reply) {
        reply('Hello, docker!');
    }
});

server.start((err) => {

    if (err)
        throw err;

    console.log('Server running at:', server.info.uri);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的。我们在端口 8080 实例化一个哈比神服务器，并在服务器启动时打印`Hello docker`。

我们可以提取现有的 nodejs docker 容器，并在其上构建我们的应用程序。或者，我们可以从头开始构建一个容器。我们将从头开始建造它。

我们将使用 [alpine](https://alpinelinux.org/) 作为我们容器的基本操作系统。为什么是阿尔卑斯山？阿尔卑斯山既小又安全。

我们将在创建上述两个文件的同一目录下创建一个名为`Dockerfile`的文本文件。`Dockerfile`包含构建容器的指令。

所以我们的目录结构看起来是这样的:

```
├── Dockerfile
├── package.json
└── server.js 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步一步地建造`Dockerfile`。开始看起来是这样的:

```
FROM alpine:3.4
RUN apk update && apk upgrade
RUN apk add nodejs
RUN rm -rf /var/cache/apk/* 
```

Enter fullscreen mode Exit fullscreen mode

这些线是什么意思？

**行 1** :我们基于阿尔卑斯山

**的容器行 2** :更新 OS

**行 3** :添加 nodejs

**行 4** :移除 apk 缓存

如果我们用这些行构建一个容器，我们将得到一个 nodejs 容器。我们想要一个幸福的家庭。所以让我们继续。

记住，我们已经创建了一个`package.json`和`server.js`文件。让我们将它们复制到容器中。将这些行添加到`Dockerfile`。

```
COPY . /src
RUN cd /src; npm install
EXPOSE 8080
CMD ["node", "/src/server.js"] 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下这几行。

**第 1 行**:我们指示 docker 引擎将整个目录复制到 docker 容器中的文件夹 src 中。

**第二行**:安装所需的依赖项。这将安装 hapijs。

**第 3 行**:注意在`server.js`中我们在 8080 运行 hapijs 服务器。在这一行中，我们指示 docker 向外界公开 8080 端口，这样我们就可以从我们的主机访问服务器。

**第 4 行**:最后我们用命令`node /src/server.js`指示 docker 运行服务器。命令和参数以这一行的方式给出。

我们的完整`Dockerfile`看起来像这样:

```
FROM alpine:3.4
RUN apk update && apk upgrade
RUN apk add nodejs
RUN rm -rf /var/cache/apk/*

COPY . /src
RUN cd /src; npm install
EXPOSE 8080
CMD ["node", "/src/server.js"] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以建造集装箱。打开终端，发出这个命令:

```
docker build -t jjude/hapi . 
```

Enter fullscreen mode Exit fullscreen mode

Docker 需要一个 docker 文件来构建一个容器。我们既可以提供完整路径，也可以指示使用当前目录中的 dockerfile。我们指示构建过程使用当前目录中的 Dockerfile 文件。

用`<user_name>/<application_name>`标记容器也是一个很好的做法。

当您发出这个命令时，它将开始从 web 上获取必要的组件(os、nodejs、hapijs)并构建一个容器。您将开始看到这样的输出:

```
Sending build context to Docker daemon 5.632 kB
Step 1/8 : FROM alpine:3.4
 ---> 0766572b4bac
Step 2/8 : RUN apk update && apk upgrade
 ---> Using cache
 ---> edfde7516f9b
Step 3/8 : RUN apk add nodejs
 ---> Running in 4d786fc5787b
(1/4) Installing libgcc (5.3.0-r0)
(2/4) Installing libstdc++ (5.3.0-r0)
(3/4) Installing libuv (1.9.1-r0)
(4/4) Installing nodejs (6.7.0-r0)
Executing busybox-1.24.2-r13.trigger
OK: 41 MiB in 15 packages
 ---> 25426b5e81e2
Removing intermediate container 4d786fc5787b
Step 4/8 : RUN rm -rf /var/cache/apk/*
 ---> Running in 2db1ab7c47b5
 ---> efbdf59a5e3a
Removing intermediate container 2db1ab7c47b5
Step 5/8 : COPY . /src
 ---> f67db160cffb
Removing intermediate container b452bcf97da6
Step 6/8 : RUN cd /src; npm install
 ---> Running in eebeb5c18070
sample-hapijs-inside-docker@1.0.0 /src
`-- hapi@14.0.0
  +-- accept@2.1.3
  | `-- boom@4.2.0
  +-- ammo@2.0.3
  | `-- boom@4.2.0
  +-- boom@3.2.2
  +-- call@3.0.4
  | `-- boom@4.2.0
  +-- catbox@7.1.3
  | +-- boom@4.2.0
  | `-- joi@10.2.0
  +-- catbox-memory@2.0.4
  +-- cryptiles@3.1.1
  | `-- boom@4.2.0
  +-- heavy@4.0.3
  | +-- boom@4.2.0
  | `-- joi@10.2.0
  +-- hoek@4.1.0
  +-- iron@4.0.4
  | `-- boom@4.2.0
  +-- items@2.1.1
  +-- joi@9.2.0
  | +-- isemail@2.2.1
  | `-- moment@2.17.1
  +-- kilt@2.0.2
  +-- mimos@3.0.3
  | `-- mime-db@1.26.0
  +-- peekaboo@2.0.2
  +-- shot@3.4.0
  | `-- joi@10.2.0
  +-- statehood@4.1.0
  +-- subtext@4.3.0
  | +-- boom@4.2.0
  | +-- content@3.0.3
  | | `-- boom@4.2.0
  | +-- pez@2.1.4
  | | +-- b64@3.0.2
  | | +-- boom@4.2.0
  | | `-- nigel@2.0.2
  | |   `-- vise@2.0.2
  | `-- wreck@10.0.0
  |   `-- boom@4.2.0
  `-- topo@2.0.2

npm WARN sample-hapijs-inside-docker@1.0.0 No repository field.
 ---> e04821112bdf
Removing intermediate container eebeb5c18070
Step 7/8 : EXPOSE 8080
 ---> Running in 6889ad302d2f
 ---> adfd88fe97ca
Removing intermediate container 6889ad302d2f
Step 8/8 : CMD node /src/server.js
 ---> Running in dcde9b8ab986
 ---> 07786de22668
Removing intermediate container dcde9b8ab986
Successfully built 07786de22668 
```

Enter fullscreen mode Exit fullscreen mode

应该以`Successfully built`结尾。

我们拿到集装箱了。可以通过发出`docker images`来检查。它应该显示如下:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
jjude/hapi          latest              07786de22668        23 minutes ago      41.1 MB 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，可以执行我们的`server.js`的基本容器是 41.1 MB。

现在我们需要打开这个容器(运行它)。发出这个命令`docker run -p 8080:8080 -d jjude/hapi`。

这个命令有什么作用？

我们正在运行我们的容器`jjude/hapi`，并将容器端口 8080 绑定到主机端口 8080。

如果您想使用不同的端口，比如 9000，该怎么办？你可以用`-p 9000:8080`来做。格式是这样的:

```
-p <host port>:<container port> 
```

Enter fullscreen mode Exit fullscreen mode

它将返回到终端命令提示符。如何检查我们的小服务器是否在运行？

发出`curl`命令。

```
curl http://localhost:8080 
```

Enter fullscreen mode Exit fullscreen mode

这应该输出这个:

```
Hello, docker! 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们成功地对我们的 hello docker 程序示例进行了 dockered。

如果你想进集装箱里玩呢？Docker 提供了一个进入运行容器的选项。为此，我们需要知道正在运行的容器的 id。发布`docker ps`。它将显示如下输出:

```
CONTAINER ID        IMAGE               COMMAND                 CREATED             STATUS              PORTS                    NAMES
2fb1cb0abb92        jjude/hapi          "node /src/server.js"   23 minutes ago      Up 23 minutes       0.0.0.0:8080->8080/tcp   cocky_hawking 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是容器 id。复制它并发出命令:

```
docker exec -it 2fb1cb0abb92 /bin/sh 
```

Enter fullscreen mode Exit fullscreen mode

使用前面输出中的正确容器 id。

现在你进入了容器。您可以停止并重新启动服务器。源代码位于`/src/`文件夹中。

您可以将这三个文件(package.json、server.js、Dockerfile)签入到一个存储库中，并要求团队成员签出该存储库并构建容器。它将以完全相同的方式工作。这就是 Docker 的魔力！

**有兴趣用 typescript 学习 hapijs？订阅我的[简讯](https://jjude.com/hapijs)**

*最初发布于[jjude.com](https://www.jjude.com/hapijs-on-docker/)T3】*