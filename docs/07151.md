# 使用 Docker Compose 开发 NodeJS

> 原文：<https://dev.to/kellyjandrews/using-docker-compose-for-nodejs-development>

> 这篇文章最初发表在 [Codeship 博客](https://blog.codeship.com/using-docker-compose-for-nodejs-development/)上，经作者允许后发表在这里。

Docker 对于开发者来说是一个神奇的工具。它允许我们在任何主机上构建和复制映像，消除了开发环境的不一致性，并大大缩短了入职时间。

为了提供一个如何转移到容器化开发的例子，我使用 NodeJS、Express 和 PostgreSQL 构建了一个简单的`todo` API，使用 [Docker Compose](https://docs.docker.com/compose/) 进行开发、测试，并最终在我的 CI/CD 管道中使用。

在这个由两部分组成的系列中，我将介绍开发和管道创建步骤。在这篇文章中，我将介绍第一部分:用 Docker Compose 开发和测试。

## 对本教程的要求

本教程要求您在开始之前拥有一些物品。

*   安装 [Docker 社区版](https://store.docker.com/search?type=edition&offering=community)
*   安装〔t0〕坞站复合〔t1〕
*   下载[待办事宜应用示例-非 Docker 分支](https://github.com/codeship-library/nodejs-express-todoapp/archive/non-docker.zip)

这里的 todo 应用程序本质上是一个替身，你可以用你自己的应用程序替换它。这里的一些设置是特定于这个应用程序的，可能没有涵盖您的应用程序的需求，但它应该是一个很好的起点，让您了解 Dockerize 您自己的应用程序所需的概念。

一旦你设置好了一切，你就可以进入下一部分了。

## 创建 Dockerfile

在任何一个 Dockerized 应用的基础上，你都会发现一个 [`Dockerfile`](https://docs.docker.com/engine/reference/builder/) 。`Dockerfile`包含所有用于构建应用程序映像的指令。您可以通过安装 NodeJS 及其所有依赖项来进行设置；然而，Docker 生态系统有一个映像存储库(Docker Store ),其中有一个已经创建并准备好使用的[节点映像。](https://store.docker.cimg/node?tab=description)

在应用程序的根目录下，创建一个新的`Dockerfile`。

```
/> touch Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的编辑器中打开新创建的`Dockerfile`。第一条指令 [`FROM`](https://docs.docker.com/engine/reference/builder/#from) ，将告诉 Docker 使用预构建的 NodeJS 映像。有几个选择，但是这个项目使用了`node:7.7.2-alpine`图像。关于我为什么在这里使用`alpine`而不是其他选项的更多细节，你可以阅读[这篇文章](https://blog.codeship.com/alpine-based-docker-images-make-difference-real-world-apps/)。

```
FROM node:7.7.2-alpine 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行`docker build .`，你会看到类似下面的内容:

```
Sending build context to Docker daemon 249.3 kB
Step 1/1 : FROM node:7.7.2-alpine
7.7.2-alpine: Pulling from library/node
709515475419: Pull complete 1a7746e437f7: Pull complete 662ac7b95f9d: Pull complete Digest: sha256:6dcd183eaf2852dd8c1079642c04cc2d1f777e4b34f2a534cc0ad328a98d7f73
Status: Downloaded newer image for node:7.7.2-alpine
 ---> 95b4a6de40c3
Successfully built 95b4a6de40c3 
```

Enter fullscreen mode Exit fullscreen mode

does 文件中只有一条指令，这不会做太多，但它确实向您展示了构建过程，而不会发生太多事情。此时，您已经创建了一个图像，运行`docker images`将显示您可用的图像:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node                7.7.2-alpine        95b4a6de40c3        6 weeks ago         59.2 MB 
```

Enter fullscreen mode Exit fullscreen mode

`Dockerfile`需要更多的指令来构建应用程序。目前它只是创建一个安装了 NodeJS 的映像，但是我们仍然需要我们的应用程序代码在容器内部运行。让我们添加更多的指令来完成这个任务，并再次构建这个映像。

这个特定的 Docker 文件使用 [`RUN`](https://docs.docker.com/engine/reference/builder/#run) 、 [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) 和 [`WORKDIR`](https://docs.docker.com/engine/reference/builder/#workdir) 。你可以在 Docker 的参考页面上阅读更多相关内容，以获得更深入的了解。

现在让我们将指令添加到`Dockerfile`中:

```
FROM node:7.7.2-alpine

WORKDIR /usr/app

COPY package.json .
RUN npm install --quiet

COPY . . 
```

Enter fullscreen mode Exit fullscreen mode

下面是正在发生的事情:

*   将工作目录设置为`/usr/app`
*   将`package.json`文件复制到`/usr/app`
*   安装`node_modules`
*   将项目根目录下的所有文件复制到`/usr/app`

您现在可以再次运行`docker build .`并查看结果:

```
Sending build context to Docker daemon 249.3 kB
Step 1/5 : FROM node:7.7.2-alpine
  ---> 95b4a6de40c3
Step 2/5 : WORKDIR /usr/app
 ---> e215b737ca38
Removing intermediate container 3b0bb16a8721
Step 3/5 : COPY package.json .
 ---> 930082a35f18
Removing intermediate container ac3ab0693f61
Step 4/5 : RUN npm install --quiet
 ---> Running in 46a7dcbba114

 ### NPM MODULES INSTALLED ###

 ---> 525f662aeacf
 ---> dd46e9316b4d
Removing intermediate container 46a7dcbba114
Step 5/5 : COPY . .
 ---> 1493455bcf6b
Removing intermediate container 6d75df0498f9
Successfully built 1493455bcf6b 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经使用 Docker 成功地创建了应用程序映像。然而，目前我们的应用程序不会做太多，因为我们仍然需要一个数据库，我们希望将所有东西连接在一起。这就是 Docker Compose 将帮助我们的地方。

## 码头工复合服务

既然您已经知道如何使用`Dockerfile`创建图像，那么让我们创建一个应用程序作为服务，并将其连接到数据库。然后，我们可以运行一些设置命令，开始创建新的待办事项列表。

创建文件`docker-compose.yml` :

```
/> touch docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

[Docker 组合文件](https://docs.docker.com/compose/compose-file/compose-file-v2/)将基于配置文件定义和运行容器。我们使用的是 [compose file version 2](https://docs.docker.com/compose/compose-file/compose-file-v2/) 语法，你可以在 Docker 的网站上阅读。

需要理解的一个重要概念是 Docker Compose 跨越了“构建时”和“运行时”到目前为止，我们一直在使用`docker build .`，即“构建时间”来构建图像。这是我们的集装箱实际建造的时间。我们可以把“运行时”想象成一旦我们的容器被构建并被使用后会发生什么。

Compose 触发“构建时”——指示我们的映像和容器进行构建——但它也填充“运行时”使用的数据，如 env 变量和卷。清楚这一点很重要。例如，当我们添加像`volumes`和`command`这样的东西时，它们将覆盖那些可能已经在“构建时”通过 docker 文件设置的东西。

在编辑器中打开您的`docker-compose.yml`文件，复制/粘贴以下行:

```
version: '2'
services:
  web:
    build: .
    command: npm run dev
    volumes:
      - .:/usr/app/
      - /usr/app/node_modules
    ports:
      - "3000:3000"
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgres://todoapp@postgres/todos
  postgres:
    image: postgres:9.6.2-alpine
    environment:
      POSTGRES_USER: todoapp
      POSTGRES_DB: todos 
```

Enter fullscreen mode Exit fullscreen mode

这需要一点时间来解释，但是让我们按服务来分解一下。

### web 服务

web 服务中的第一个指令是基于我们的`Dockerfile`来`build`图像。这将重新创建我们以前使用的图像，但现在它将根据我们所在的项目命名为`nodejsexpresstodoapp`。之后，我们会给这项服务一些具体的指示，告诉它应该如何操作:

*   `command: npm run dev` -一旦构建了映像，并且容器正在运行，`npm run dev`命令将启动应用程序。
*   `volumes:` -此部分将安装主机和容器之间的路径。
*   这将把根目录挂载到我们在容器中的工作目录。
*   `/usr/app/node_modules` -这将使用构建时目录将`node_modules`目录挂载到主机上。
*   `environment:` -应用程序本身期望环境变量`DATABASE_URL`运行。这是以`db.js`为背景的。
*   `ports:` -这将向主机发布容器的端口，在本例中为`3000`，作为端口`3000`。

`DATABASE_URL`是连接字符串。`postgres://todoapp@postgres/todos`使用`todoapp`用户连接，在主机`postgres`上，使用数据库`todos`。

### Postgres 服务

像我们使用的 NodeJS 映像一样，Docker 存储有一个为 [PostgreSQL](https://store.docker.cimg/022689bf-dfd8-408f-9e1c-19acac32e57b?tab=description) 预先构建的映像。我们可以使用图像的名称，而不是使用`build`指令，Docker 将为我们获取并使用该图像。在这种情况下，我们使用的是`postgres:9.6.2-alpine`。我们可以让它保持原样，但是它有`environment`变量，让我们可以对它进行一些定制。

这个特殊的图像接受几个环境变量，所以我们可以根据自己的需要定制。`POSTGRES_USER: todoapp` -这会将用户`todoapp`创建为 PostgreSQL 的默认用户。`POSTGRES_DB: todos` -这将创建默认数据库为`todos`。

## 运行应用程序

现在我们已经定义了我们的服务，我们可以使用`docker-compose up`构建应用程序。这将显示正在构建并最终开始的图像。在初始构建之后，您将看到正在创建的容器的名称:

```
Pulling postgres (postgres:9.6.2-alpine)...
9.6.2-alpine: Pulling from library/postgres
627beaf3eaaf: Pull complete e351d01eba53: Pull complete cbc11f1629f1: Pull complete 2931b310bc1e: Pull complete 2996796a1321: Pull complete ebdf8bbd1a35: Pull complete 47255f8e1bca: Pull complete 4945582dcf7d: Pull complete 92139846ff88: Pull complete Digest: sha256:7f3a59bc91a4c80c9a3ff0430ec012f7ce82f906ab0a2d7176fcbbf24ea9f893
Status: Downloaded newer image for postgres:9.6.2-alpine
Building web
...
Creating nodejsexpresstodoapp_postgres_1
Creating nodejsexpresstodoapp_web_1
...
web_1       | Your app is running on port 3000 
```

Enter fullscreen mode Exit fullscreen mode

此时，应用程序正在运行，您将在控制台中看到日志输出。您还可以使用`docker-compose up -d`将服务作为后台进程运行。在开发过程中，我更喜欢在没有`-d`的情况下运行，并创建第二个终端窗口来运行其他命令。如果你想在后台运行它并查看日志，你可以运行`docker-compose logs`。

在新的命令提示符下，您可以运行`docker-compose ps`来查看正在运行的容器。您应该会看到类似下面的内容:

```
 Name                            Command              State           Ports
------------------------------------------------------------------------------------------------
nodejsexpresstodoapp_postgres_1   docker-entrypoint.sh postgres   Up      5432/tcp
nodejsexpresstodoapp_web_1        npm run dev                     Up      0.0.0.0:3000->3000/tcp 
```

Enter fullscreen mode Exit fullscreen mode

这将告诉您服务的名称、用于启动它的命令、它的当前状态以及端口。注意`nodejsexpresstodoapp_web_1`已经将端口列为`0.0.0.0:3000->3000/tcp`。这告诉我们，您可以在主机上使用`localhost:3000/todos`来访问应用程序。

```
/> curl localhost:3000/todos

[] 
```

Enter fullscreen mode Exit fullscreen mode

`package.json`文件有一个脚本来自动构建代码并将模式迁移到 PostgreSQL。只要不删除`postgres:9.6.2-alpine`映像，模式和容器中的所有数据都将保持不变。

然而，最终，检查一下你的应用程序如何通过干净的设置来构建会是一件好事。你可以运行`docker-compose down`，它会清除已经构建好的东西，让你看到正在发生的事情有一个新的开始。

请随意查看源代码，尝试一下，看看事情进展如何。

## 测试应用程序

应用程序本身包含一些使用 [`jest`](https://facebook.github.io/jest/) 构建的集成测试。有多种方法可以进行测试，包括为测试环境创建类似于`Dockerfile.test`和`docker-compose.test.yml`的文件。这有点超出了本文的范围，但是我想向您展示如何使用当前的设置运行测试。

当前容器正在使用项目名`nodejsexpresstodoapp`运行。这是默认的目录名。如果我们试图运行命令，它将使用相同的项目，容器将重新启动。这是我们不想要的。

相反，我们将使用不同的项目名称来运行应用程序，将测试隔离到它们自己的环境中。因为容器是短暂的，所以在一组单独的容器中运行你的测试可以确保你的应用程序在一个干净的环境中完全正常地运行。

在您的终端中，运行以下命令:

```
/> docker-compose -p tests run -p 3000 --rm web npm run watch-tests 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到`jest`运行集成测试并等待更改。

`docker-compose`命令接受几个[选项](https://docs.docker.com/compose/reference/overview/)，后跟一个命令。在这种情况下，您使用`-p tests`来运行`tests`项目名称下的服务。正在使用的命令是 [`run`](https://docs.docker.com/compose/reference/run/) ，将对一个服务执行一次命令。

由于`docker-compose.yml`文件指定了一个端口，我们使用`-p 3000`创建一个随机端口来防止端口冲突。当我们停止集装箱时，`--rm`选项将移除集装箱。最后，我们正在运行`web`服务`npm run watch-tests`。

## 结论

此时，您应该已经有了使用 Docker Compose 进行本地应用程序开发的良好开端。在本系列关于使用 Docker Compose 进行 NodeJS 开发的下一部分中，我将介绍使用 Codeship 集成和部署这个应用程序。

你的团队在开发工作流程中使用 Docker 了吗？如果是这样的话，我很想听听你正在做什么，以及你从中看到了什么好处。