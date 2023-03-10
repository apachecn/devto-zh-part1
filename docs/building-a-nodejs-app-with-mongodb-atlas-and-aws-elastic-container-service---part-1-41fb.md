# 用 MongoDB Atlas 和 AWS 弹性容器服务构建 NodeJS 应用程序——第 1 部分

> 原文：<https://dev.to/mongodb/building-a-nodejs-app-with-mongodb-atlas-and-aws-elastic-container-service---part-1-41fb>

构建应用程序从未如此之快，这在很大程度上要归功于虚拟化的进步。最近，我一直在探索更好的方法来构建和部署应用程序，而不必担心持续的前端工作，例如修补 Linux 机器，在它们出现故障时替换它们，甚至不得不 SSH 到系统。

在这个由两部分组成的教程中，我将分享如何使用 MongoDB Atlas 构建 Node.js 应用程序，并使用 Amazon EC2 容器服务(ECS)轻松部署它。

在第一部分中，我们将介绍:

*   使用 Docker 进行应用部署
*   构建 MongoDB Atlas 集群
*   将它连接到基于 Node.js 的应用程序，该应用程序允许完整的 CRUD 操作
*   在开发环境中本地启动它。

[在第二部分](https://www.mongodb.com/blog/post/building-a-nodejs-app-with-mongodb-atlas-and-aws-elastic-container-service-part-2?utm_medium=dev-synd&utm_source=dev&utm_content=container2&jmp=dev-ref)，我们将通过安装一些工具、配置一些环境变量和启动 Docker 集群，让我们的应用在 AWS 上的 Linux 容器中运行。

在这个由两部分组成的教程结束时，您将看到如何使用 Node.js 启动一个应用程序，如何为该应用程序构建一个 MongoDB 集群，最后，如何使用 AWS 在容器中部署该应用程序。

### Docker 快速入门

***(已经了解集装箱&码头工人？向前跳到教程。)***

Docker 使开发人员能够在不同的环境中轻松构建、发布和运行分布式应用程序。它通过帮助您“容器化”您的应用程序来做到这一点。

什么是容器？Docker 网站将容器定义为“[一种将软件打包成可以在共享操作系统上独立运行的格式的方式](https://www.docker.com/what-docker)。更广泛地说，容器是一种系统虚拟化的形式，允许您在资源隔离的进程中运行应用程序及其依赖项。使用容器，您可以轻松地将应用程序代码、配置和依赖项打包到易于使用的构建块中，这些构建块是专门为运行您的代码而构建的。这可确保您在任何部署环境下都能快速、可靠且一致地进行部署，并让您能够更专注于构建您的应用程序。此外，容器使管理微服务和支持 CI/CD 工作流变得更加容易，在这些工作流中，增量更改被隔离、测试和发布，对生产系统几乎没有影响。

### 入门

在本文的示例中，我们将使用 MongoDB、Express.js、React.js 和 Node.js 构建一个简单的记录系统，名为 [mern-crud](https://github.com/cefjoeii/mern-crud) 。我们将在 AWS 云平台上组合几个常见的服务，构建一个具有自动伸缩和负载平衡功能的全功能容器化应用程序。我们前端的核心将运行在亚马逊 EC2 [集装箱服务(ECS)](https://aws.amazon.com/ecs/) 上。

在第一部分中，我将设置我们的环境，并在我的本地工作站上构建应用程序。这将向您展示如何开始使用 MongoDB Atlas 和 Node.js。在第二部分中，我们将通过使用 coldbrew-cli(一个简单的 ECS 命令行实用程序)部署到 ECS 来完成所有工作。

下面您将看到构建一个允许您在 MongoDB 中执行 CRUD 操作的应用程序的基础。

要求:

*   [AWS 账户](https://aws.amazon.com/)
*   [IAM 访问键](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)
*   [coldbrew-cli](https://github.com/coldbrewcloud/coldbrew-cli)
*   [MongoDB Atlas 集群](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=container1&jmp=dev-ref) (M0，Atlas 的自由层就好)
*   [Docker](https://www.docker.com/) (在本地计算机上)
*   [mern-crud github repo](https://github.com/cefjoeii/mern-crud)
*   [去](https://github.com/git)
*   [Node.js](https://nodejs.org) 本地安装硼 LTS(6 . 11 . 2)

Amazon EC2 容器服务(ECS)允许您使用 AWS 控制面板部署和管理 Docker 实例。关于它如何工作的完整分类，我建议查看 Abby Fuller 的“ECS 入门”演示，其中包括底层 Docker 部署的每个部分的角色的详细信息，以及它们如何协同工作来为您提供易于重复和部署的应用程序。

### 配置 AWS 和 coldbrew-cli

#### AWS 环境变量

首先注册您的 AWS 帐户，并创建您的身份和访问管理(IAM)密钥。您可以从 coldbrew-cli 文档中查看所需的权限[。需要 IAM 密钥来根据 AWS API 进行身份验证；我们可以在运行 coldbrew-cli 命令的命令 shell 中将这些导出为环境变量:](https://github.com/coldbrewcloud/coldbrew-cli/wiki/AWS-IAM-Policies) 

```
export AWS_ACCESS_KEY_ID="PROVIDEDBYAWS
export AWS_SECRET_ACCESS_KEY="PROVIDEDBYAWS 
```

Enter fullscreen mode Exit fullscreen mode

这允许 coldbrew-cli 使用我们在 AWS 中的用户权限来创建适当的底层基础架构。

#### cold brew-CLI 入门

接下来，我们将使用 coldbrew-cli，这是一个用于部署 AWS Docker 容器的开源自动化实用程序。它删除了许多与创建 Docker 映像、创建环境以及自动扩展与 AWS 上的 Node.js web 服务器相关的 AWS 组相关的步骤。coldbrew 最好的部分是它的高度可移植性和可配置性。 [coldbrew 文档](https://github.com/coldbrewcloud/coldbrew-cli)陈述如下:_coldbrew-cli 基于两个简单的概念运行:应用程序(apps)和集群。

*   应用程序是最小的部署单元。
*   一个或多个应用可以在一个集群中运行，并且它们共享计算资源。_ 我们将使用该应用工具来简化创建生产 ECS 群集时必须执行的许多常见任务。它将代表我们处理以下事项:
*   创建一个 [VPC](https://aws.amazon.com/vpc/) (如果需要，您可以指定一个现有的 VPC)
*   创建[安全组](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html)
*   设置[弹性负载均衡](https://aws.amazon.com/elasticloadbalancing/) & [自动伸缩组](http://docs.aws.amazon.com/autoscaling/latest/userguide/AutoScalingGroup.html)
*   建造[码头集装箱](https://www.docker.com/what-docker)
*   建立码头工人形象
*   将 [Docker 图像推送到 ECR 存储库](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_Console_Repositories.html)
*   手动将映像部署到容器，上面列出的所有任务都需要付出巨大的努力。要安装 coldbrew-cli，我们可以[下载 cli 可执行文件](https://github.com/coldbrewcloud/coldbrew-cli/wiki/Downloads) (coldbrew 或 coldbrew.exe)并将其放入我们的`$PATH.`

```
$ which coldbrew
'usr/local/bin/coldbrew 
```

Enter fullscreen mode Exit fullscreen mode

### 准备好我们的 app 和 MongoDB 图集

ECS 将允许我们运行一个无状态的应用程序前端。我们所有的数据都将存储在 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=container1&jmp=dev-ref) 中，这是在 AWS 上运行 MongoDB 的最佳方式。在本节中，我们将构建我们的数据库集群并连接我们的应用程序。我们可以依靠 coldbrew 命令来管理未来新版本应用程序的部署。

#### 你的图集簇

构建 Atlas 集群既简单又免费。如果你在这方面需要帮助，[看看我制作的这个视频教程](https://www.youtube.com/watch?v=_d8CBOtadRA)，它将带你完成这个过程。一旦我们的免费 M0 集群构建完成，很容易[将我们的 ECS 容器](https://docs.atlas.mongodb.com/setup-cluster-security/)的 IP 地址列入白名单，这样我们就可以在 MongoDB 中存储数据。

[![](img/061919caedc4a385468fd14b3bdd12ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w83CkGt7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image6-sze2unklrv.png)

我把我的集群命名为`"mern-demo”`。

#### 下载和配置 App

让我们从下载所需的[存储库](https://github.com/cefjoeii/mern-crud) :
开始

```
$ git clone git@github.com:cefjoeii/mern-crud.git
Cloning into 'mern-crud'...
remote: Counting objects: 303, done.
remote: Total 303 (delta 0), reused 0 (delta 0), pack-reused 303
Receiving objects: 100% (303/303), 3.25 MiB | 0 bytes/s, done.
Resolving deltas: 100% (128/128), done. 
```

Enter fullscreen mode Exit fullscreen mode

让我们进入我们克隆的存储库，看看代码:

[![](img/17f0b07a8c3ff1851cfe3e60092422fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0LFNogHC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image7-8wudnh3vqn.png)

`server.js`应用依赖于存储在`config/db.js`文件中的数据库配置。我们将使用它来指定我们在 MongoDB Atlas 中的 M0 集群，但是我们希望确保我们使用遵循最小特权原则的用户凭证来这样做。

#### 保护我们的应用

让我们创建一个基本的数据库用户，以确保我们没有授予应用程序管理数据库的全部权限。这个基本用户需要的只是读写权限。

在 MongoDB Atlas UI 中，点击屏幕顶部的“安全”选项，然后点击进入“MongoDB 用户”

[![](img/7b5e95c944bf7ea28e8963036770cbad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ckf3T_-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image4-r2x9wyc5ct.png)

现在，单击“Add new user”按钮，创建一个用户，该用户对我们的集群中要写入数据的命名空间具有读写权限。

[![](img/2d8a1dd39e2f8a12d00926af281b65d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5HQcjuKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image2-3o5zaar73q.png)

*   输入用户名(我将选择 mernuser)
*   单击“显示高级选项”链接
*   在“数据库”部分的“merndb”中选择角色“读写”，并将“集合”留空
*   创建一个密码并将其保存在某个地方，以便在连接字符串中使用
*   点击“添加用户”

现在，我们有一个用户可以访问我们的应用程序的 MongoDB Atlas 数据库集群了。是时候获取我们的连接字符串并开始准备应用程序了。

#### 将我们的托管集群链接到我们的应用

我们的连接字符串可以通过访问 Atlas 中的“集群”部分，然后单击“连接”按钮来访问。

本节提供了一个将 IP 地址和连接到集群的方法列入白名单的界面。让我们选择“连接您的应用程序”选项，并复制我们的连接字符串。让我们复制连接字符串，看看我们需要修改什么:

默认的连接字符串为我们提供了一些需要我们修改的部分，下面用红色突出显示:

```
mongodb://mernuser:demopass@mern-demo-shard-00-00-x8fks.mongodb.net:27017,mern-demo-shard-00-01-x8fks.mongodb.net:27017,mern-demo-shard-00-02-x8fks.mongodb.net:27017/merndb?ssl=true&replicaSet=mern-demo-shard-0&authSource=admin 
```

Enter fullscreen mode Exit fullscreen mode

用户名和密码应该替换为我们之前创建的凭证。我们还需要指定存储数据的`merndb`数据库。

让我们将连接字符串添加到`config/db.js`文件中，然后编译我们的应用程序。

[![](img/f475902477b2820fc2b68072f7825d3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z8pm3_kO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image8-vj74l35tzd.png)

保存文件，然后使用“`npm install`”命令从存储库的根目录导入应用程序所需的依赖项:

[![](img/9609fce1c0d630d9bae9c4bb4bc9fad9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IkAlRCQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image5-9x38sry0qh.gif)

让我们将我们的本地 IP 地址列入白名单，这样我们就可以启动我们的应用程序，并在部署到 Docker 之前测试它在本地的工作情况。我们可以在 MongoDB Atlas UI 中单击“CONNECT ”,然后选择“ADD CURRENT IP ADDRESS”将我们的本地工作站加入白名单:

[![](img/5b5420902c840ac02a478bef12345f0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NyCua7UA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image3-5ib212lezj.png)

我们现在可以测试应用程序，并看到它像预期的那样启动:

```
$ npm start
> mern-crud@0.1.0 start /Users/jaygordon/work/mern-crud
> node server

Listening on port 3000
Socket mYwXzo4aovoTJhbRAAAA connected.
Online: 1
Connected to the database. 
```

Enter fullscreen mode Exit fullscreen mode

让我们在浏览器中测试一下*[http://localhost:3000](http://localhost:3000)*:

[![](img/42e2d769264ed67a726460aa89a7f7ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DYWX_EUh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image1-c0o61nfd3o.gif)

最难的部分已经完成，接下来的部分是用`coldbrew-cli`进行部署，这只是几个命令。在本系列的第二部分中，我们将介绍如何使用 coldbrew-cli 启动，然后在完成后销毁我们的应用程序。

* * *

如果您不熟悉托管 MongoDB 服务，我们鼓励您从我们的[免费层](https://www.mongodb.com/cloud/atlas)开始。对于第三方服务提供商的现有客户，请务必查看我们的迁移服务，并了解如何获得 [3 个月的免费服务](https://www.mongodb.com/cloud/atlas/lp/compare/mlab)。