# 了解 Docker 容器

> 原文：<https://dev.to/stephenafamo/understanding-docker-containers-43id>

[![Docker container](img/252aba40663818a99065b504cd35149b.png "Docker container")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eOGf-5pj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-container.png%3Ffit%3D709%252C247%26ssl%3D1)

在[之前的文章](https://scotch.io/bar-talk/working-effectively-with-docker-images)中，我们讨论了 Docker 图像，但是我们只能用一小部分来讨论 Docker 容器。现在，让我们更深入。

## 码头工人集装箱

Docker 容器是从 Docker 映像创建的。如果 Docker 图像是一个菜谱，那么可以把 Docker 容器想象成准备好的菜肴

> 容器映像是一个轻量级的、独立的、可执行的软件包，包含了运行它所需的一切:代码、运行时、系统工具、系统库、设置。
> 
> 容器将软件从其周围环境中隔离出来，例如开发和阶段环境之间的差异，并有助于减少在同一基础设施上运行不同软件的团队之间的冲突。

因此，要真正利用 Docker 映像，我们必须运行它。当我们运行 Docker 映像时，我们创建一个 Docker 容器。

## 运行 Docker 镜像

运行 Docker 映像的基本方式是通过`docker run`命令。

> Docker 在隔离的容器中运行进程。容器是在主机上运行的进程。主机可以是本地或远程的。当操作员执行 docker run 时，运行的容器进程是独立的，因为它有自己的文件系统、自己的网络和自己的独立于主机的进程树。

有一个[完整的文档](https://docs.docker.com/engine/reference/run/)，详细说明了你可以添加到`docker run`命令的所有选项。我们将只是浏览一下我们经常使用的那些。

在本文中，我们将使用我们在[上一篇](https://scotch.io/bar-talk/working-effectively-with-docker-images)文章中创建的 AdonisJs 图像。 [`stephenafamo/adonisjs`](https://hub.docker.com/r/stephenafamo/adonisjs/) 的形象。

## 指定 Docker 图像

`docker run`命令至少需要一个参数，那就是您想要运行的图像。比如说。

```
docker run stephenafamo/adonisjs 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以指定想要运行的图像的标签。像这样。

```
docker run stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

为了查看正在运行的容器，我们使用了`docker ps`命令。如果我们想看到所有的容器，我们使用；

```
docker ps -a 
```

Enter fullscreen mode Exit fullscreen mode

[![docker ps](img/5b97b8b6fe418aa55cee125dc2420aab.png "docker ps")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8p_hp51J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-ps.png%3Fw%3D980%26ssl%3D1)

> **注意**:为了查看一个容器的所有信息，我们通过运行`docker inspect container_name`来检查它
> 
> ## 命名容器

当我们列出正在运行的 Docker 容器时，最后一列是容器的名称。默认情况下，Docker 会分配一个随机字符串作为容器的名称。

我们可以使用`--name`标志指定容器的名称。像这样；

```
docker run --name adonis stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行`docker ps`时，我们可以看到我们的容器被正确命名。

[![docker ps named](img/8329198338f85ae7b032358d5004c500.png "docker ps named")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TPcTjS6b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-ps-named.png%3Fw%3D980%26ssl%3D1)

## 前景或背景

您现在应该已经注意到，当我们运行 adonis 容器时，它开始在终端中执行，您可能必须停止该进程，或者打开一个新的终端来检查容器。这是因为容器在前台运行。

通过添加`-d`或`--detach`标志，我们可以让我们的容器以分离模式在后台运行。像这样；

```
docker run --name adonis -d stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

如果您不需要容器来持久化，也就是说，如果您希望 Docker 在容器退出时自动清理容器并删除文件系统，那么您应该运行带有`--rm`标志的容器。

```
docker run --name adonis --rm stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

## 约束资源

当您运行多个 Docker 容器时，您可以决定限制特定容器可用的资源量。默认情况下，它会尝试使用主机的全部资源。完整的列表在这里[可以找到](https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources)，让我们只看那些经常使用的。

### 记忆

我们可以通过使用`-m`或`--memory`标志来指定可用内存的数量。

[![docker run -m](img/557606504fd111fd847fade1e97afecf.png "docker run -m")](https://res.cloudinary.com/practicaldev/image/fetch/s--Z123tGWW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-m.png%3Fw%3D980%26ssl%3D1)T3】

```
docker run --name adonis -d -m 4g stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

### 记忆和互换

我们可以通过使用`--memory-swap`标志来指定可用内存的数量。

[![docker run --memory-swap](img/d1d892b0313db977e4c6c236c30e70d5.png "docker run --memory-swap")](https://res.cloudinary.com/practicaldev/image/fetch/s--jdqX255R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-memory-swap.png%3Fw%3D980%26ssl%3D1)T3】

```
docker run --name adonis -d -m 4g --memory-swap 8g stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:使用内存交换限制时，您应该始终设置内存限制
> 
> ### CPU

我们可以用`--cpus`标志来限制一个容器可以使用的 CPU 数量。

[![docker run --cpus](img/72fb51540f306359f43658f550641d09.png "docker run --cpus")](https://res.cloudinary.com/practicaldev/image/fetch/s--flyL-ye9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-cpus.png%3Fw%3D980%26ssl%3D1)T3】

```
docker run --name adonis -d --cpus 2 stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

### CPU 份额

我们可以使用不同容器的相对权重，并允许 Docker 适当地限制处理能力，而不是手动跟踪每个容器使用的 CPU 数量。

我们使用`-c`或`--cpu-shares`标志来实现这一点。

[![docker run --cpu-shares](img/b4b9721e8ed805e4967acd2384c32992.png "docker run --cpu-shares")](https://res.cloudinary.com/practicaldev/image/fetch/s--3RiXscRm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-c.png%3Fw%3D980%26ssl%3D1)T3】

```
docker run --name adonis -d --cpu-shares 2 stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

## 端口

在创建 Docker 映像时，通常会暴露一些端口。这是通过 Dockerfile 中的`EXPOSE`指令完成的。

我们可以使用`docker run`命令的`--expose`标志来公开额外的端口。

```
docker run --name adonis -d \
    --expose 80 --expose 443 \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以通过“发布”这些端口，将主机上的一些端口绑定到 docker 容器中的端口。

如果我们将`-P`(大写)或`--publish-all`标志添加到我们的`docker run`命令中，所有暴露的端口将被公布，并从一系列端口中随机分配给主机上的端口。

```
docker run --name adonis -d \
    --expose 80 --expose 443 \
    -P \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`p`(小写)或`--publish`标志来指定我们想要发布的端口。

```
docker run --name adonis -d \
    --expose 80 --expose 443 -p 80 \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

我们可以进一步指定主机上的哪个端口应该这样映射；

```
docker run --name adonis -d \
    --expose 80 --expose 443 \
    -p 32277:80 -p 23458:443 \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果我们使用`-p`标志在 docker 容器中指定一个端口，我们不需要暴露它，因为如果它以前没有被暴露，它将被自动暴露。所以我们可以这样做:

```
docker run --name adonis -d \
    -p 32277:80 -p 23458:443 \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

## 环境变量

许多 Docker 图像利用环境变量来确定 Docker 容器在运行时的行为。此外，我们可能需要为我们打算在容器中运行的应用程序指定环境变量。

例如，`stephenafamo/adonisjs:1.0.0` images 使用一个环境变量向容器启动时运行的`adonis install`命令添加任何标志。

当使用`-e`或`--env`标志创建容器时，我们可以很容易地设置容器的环境变量。

```
docker run --name adonis \
    -d \
    -e "adonisFlags=--slim --yarn" \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

出于安全或协作的考虑，我们可能更喜欢将环境变量保存在一个单独的地方。在这种情况下，我们可以使用`--env-file`标志来指定一个包含环境变量的文件。

```
docker run --name adonis \
    -d \
    --env-file /path/to/env/file \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:我们可以多次使用`-e`和`--env-file`标志来指定多个环境变量或 env 文件。
> 
> ## 卷

当我们需要持久化数据时，Docker 卷是非常宝贵的。因为 Docker 容器中的所有数据都会在容器被移除时被销毁，所以我们在很多情况下都需要使用卷。

我们将在后面详细讨论 Docker 卷。我们可以创建卷，将它们附加到一个或多个容器，即使容器被移除，卷中的数据也将持续存在。

为了本文简单起见，我们将简单地在 Docker 容器中挂载一个目录。

挂载一个目录是持久化数据的好方法，因为即使容器被删除，该目录中的文件也会保留。

我们可以使用`-v`或`--volume`标志挂载一个目录。

```
docker run --name adonis \
    -d \
    -v /path/to/mount/directory:/path/to/destination/in/container \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

## 重启

几个原因可以导致我们的 Docker 容器退出。我们可以使用`--restart`标志设置当容器退出时会发生什么。选项包括“否”、“总是”、“除非停止”或“故障时”。

[![docker restart options](img/4884e2a18f488297ffbc0e635ad3908e.png "docker restart options")](https://res.cloudinary.com/practicaldev/image/fetch/s--fbrL8wlh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-restart.png%3Fw%3D980%26ssl%3D1)T3】

```
docker run --name adonis \
    -d \
    --restart always \
    stephenafamo/adonisjs:1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

## 联网

我们可以使用各种标志来修改容器的网络设置。当你需要定制你的容器如何相互通信时，这特别有用。正确的网络配置允许我们做一些真正强大的事情，例如[这个](https://stephenafamo.com/blog/easy-powerful-reverse-proxy-load-balancing-docker/)。网络标志的完整列表可以在[这里](https://docs.docker.com/engine/reference/run/#network-settings)找到。

[![docker run network settings](img/8c42ea842f09fda55169e5e398a7b551.png "docker run network settings")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XZevSJgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/docker-run-network-settings.png%3Fw%3D980%26ssl%3D1)

Docker 中的网络是我们将在后续文章中详细讨论的另一个领域。

## 结论

我们已经介绍了如何运行 Docker 容器，以及定制容器行为的许多方法。

我希望这能让你更舒服地使用 Docker。我相信这是一个很好的工具。

一如既往，我很欣赏评论，建议和更正。

帖子[了解 Docker 容器](https://stephenafamo.com/blog/understanding-docker-containers/)首先出现在 [Stephen AfamO 的博客](https://stephenafamo.com/blog)上。