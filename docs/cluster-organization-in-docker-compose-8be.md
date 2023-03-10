# Docker Compose 中的集群组织

> 原文：<https://dev.to/dmfay/cluster-organization-in-docker-compose-8be>

这里我长话短说:去年的这个时候，我对容器或编排一无所知，除了那个听起来很酷的想法，但在实践中并没有为我做太多。但是我们有一个架构师，他比 T1 懂得更多，他用一个非常漂亮的基于 Kubernetes 和 Docker 的构建和部署系统来设置我们的应用程序(以后可能会有更多的介绍)。我们的开发和 QA 环境变成了 Kubernetes 集群，我开始了解它是如何工作的，一切都很好。然后他继续前进，让我和另一个和我一样了解周围事物的同事成为这里一切云的事实上的专家。哎呀。

建筑师和我都没有预料到的一件事是，我们的许多企业客户最终没有加入 Kubernetes。一点也不。他们中的一些人甚至对码头时期感到不舒服，但是在这方面除了等待没有太多事情可做。对于其余的，我们决定编排使事情变得如此简单，我们将在任何我们可以摆脱它的地方做它，所以我们需要准备好 Docker 组合定义。

我继承了一些基本的 Compose 配置，但是它们已经严重过时了；在此期间，我们添加了几个 Postgres 扩展，集成了一个单点登录服务，进行了一系列的重组——你知道它是如何进行的。所以我最终回到了绘图板的所有最复杂的位。在这个过程中，我发现有些事情我在 Kubernetes 上认为是理所当然的，而在 Compose 上却不能。

### 艰难的开始

比如乔布斯。我快要非常想念乔布斯了。

在 Kubernetes，乔布斯让你运行一次性任务。我们使用该功能来支持数据库、运行迁移，并为开发环境植入初始数据，因为我们每天都要重新生成这些数据。它工作得很好:部署 pods 反弹，直到数据库初始化。如果发生了意想不到的事情，杀死豆荚和 Kubernetes 开始为你另一个。到目前为止，一切顺利。

Docker Compose 不会这样做。在 Docker Compose 中，启动的东西应该保持不变，否则就会被替换。这是个问题。我在寻找一种方法来发出一个单独的`docker-compose up`并拥有一个全新的集群，所有复杂的一次性初始化工作都为我完成了。很容易扩展应用服务器映像入口点来完成所有的初始化，但是每个集群在一个负载均衡器后面运行两到三个这样的入口点，所以这样做可能会导致启动代码多次运行而产生不一致的结果。

细分来看，当集群启动时，应用程序服务和数据库之间需要发生的所有事情如下:

1.  如果 Postgres 还没有运行，不要启动任何应用服务。
2.  如果应用程序数据库角色不存在，请创建它们。
3.  如果应用程序数据库不存在，请创建它。
4.  将任何未完成的迁移脚本部署到数据库。
5.  如果单一登录的数据库基础结构不存在，请创建它。
6.  将任何新内容部署到数据库。
7.  为每个应用程序容器中的新内容更新区域设置文件。
8.  将配置应用于每个应用程序容器。
9.  启动应用程序服务。

第六点之前的所有事情只需要发生一次。但是有了 Docker Compose，我们就没有一次性了。所以这一切都必须放在入口点脚本中，或者足够接近；我们只需确保只有一个应用程序服务可以执行敏感部分，并且它的对等服务在启动之前会等待这种情况发生。

### 调度启动

要解决的第一个问题是确保在数据库出现之前不会出现任何问题。在 Kubernetes 中，我们使用 init 容器来完成这个任务:安装作业和应用服务器部署都声明一个 init 容器，它每隔几秒钟就尝试一次`select 1`，直到成功。据我所知，Docker Compose 没有这样的东西；它最多是从您的`links`和`depends_on`以及一些其他服务属性生成一个依赖图。这确保了服务以特定的顺序启动*，但是因为 Postgres 需要几秒钟才能启动，所以依赖容器实际上可能在它准备好之前就完成了启动。*

 *确保没有东西试图与 Postgres 对话，直到它准备好为止的方法是包装启动命令。Docker 编写文档推荐了几个选项；我和[一起去等待](https://github.com/vishnubob/wait-for-it)。在撰写配置中看起来是这样的:

```
entrypoint: ["./wait-for-it.sh", "postgres:5432", "--", "bash", "./entrypoint.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

我们的`entrypoint.sh`不会运行，除非 Postgres 容器开始监听它的默认端口 5432。这很好，但还有一件事使它真正有用:因为我们已经定义了多个应用服务(Swarm 没有保证，所以我们不能设置复制模式)，我们可以选择其中一个等待 Postgres 出现，让其余的等待 *it* 依次出现。这是我们的初始化容器。

### 秘密

在这一点上，我们可以确保在数据库准备好之前，任何依赖于数据库的东西都不会启动，并且我们的一个应用程序服务将总是在任何其他应用程序服务启动之前完成启动。我们现在需要的是一种将该服务与其他服务区分开来的方法，这样它就可以执行我们曾经执行过的任务。这就是秘密的来源。

Kubernetes 和 Docker Compose 之间的秘密基本上是相同的概念:包含敏感数据的文件被加载到节点上并挂载到容器文件系统。这比使用环境变量更安全。机密被定义为构成配置中的顶级块:

```
secrets:
  db_owner_password:
    file: ./secrets/db_owner_password.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后附加到每个服务定义:

```
appserver:
    image: myimage
    links:
      - postgres
    secrets:
      - db_owner_password
    entrypoint: ["./wait-for-it.sh", "postgres:5432", "--", "bash", "./entrypoint.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

依赖的应用服务不需要`db_owner_password`；这只需要初始化数据库。因此，我们可以测试我们的入口点脚本中是否存在秘密，并且只有当它存在时才删除它:

```
if [-a /run/secrets/db_owner_password]; then
  # check and create the application roles and database, then run the migrations
fi 
```

Enter fullscreen mode Exit fullscreen mode

现在,`appserver`服务是独一无二的，我们已经限制了向它存放数据库的能力。我们不能完全粗心大意——如果`appserver`每次启动时都盲目地发出一个`createdb`,那么在第一次之后，每次都会失败，并出现“数据库已经存在”的错误——但是因为我们已经保证一次只会有一个容器*尝试*创建数据库，所以我们可以简单地预先检查。

这样就剩下了共享的配置和内容，这两者加在一起就不仅仅是秘密要处理的事情了。

### 卷

将信息从主机系统装载到容器中是一个非常普遍的用例。秘密涵盖了其中的一个特定子集。对于其他任何东西，都有数量(同样，Kubernetes 的概念版本是一个非常接近的类比)。因为卷可能比秘密大得多，所以它们不会自动在节点间共享；您必须显式创建一个命名卷，并使用支持多主机的驱动程序。

为配置和内容声明命名卷:

```
volumes:
  app_conf:
    driver: local # this is obviously not multi-host aware, but it's good enough for testing
  app_content:
    driver: local 
```

Enter fullscreen mode Exit fullscreen mode

然后在`appserver`服务定义中，添加一个`volumes`块:

```
volumes:
  - app_conf:/home/appserver/app/conf
  - app_content:/home/appserver/app/content 
```

Enter fullscreen mode Exit fullscreen mode

如果这些卷不存在，Docker Compose 将创建它们，或者您可以提前`docker volume create`创建它们。最好选择后者，否则第一次启动集群时，由于卷是空的，所有东西都会死得很惨。如果您手动创建它们，您可以`docker volume inspect`它们，在主机系统上找到挂载点，并在开始运行之前复制实例配置和内容。

一个警告:名称`app_conf`和`app_content`实际上不是 Docker Compose 寻找的名称。Compose 将`docker_`添加到您提供的名称前面，因此卷应该被命名为`docker_app_conf`和`docker_app_content`。

### 结束

从开始到结束，我花了几个星期才建立起我的第一个真正的 Compose 集群。即使 Docker 和 Compose 文档非常好，入门也很难；有很多东西需要你去理解，有很多概念你真的需要靠蛮力去理解。当时我还有很多其他的事情要做(现在也是！)，这当然也于事无补。

好消息是，昨天我不得不从头开始设置另一个具有类似编写配置的应用程序。这一次，我在几个小时内就让它运行起来了。一旦你把结构搞清楚了，明白了各个部分是如何组合在一起的，事情就好办多了。*