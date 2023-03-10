# Apache NiFi——如何部署我的流？

> 原文：<https://dev.to/bbende/apache-nifi-how-do-i-deploy-my-flow-2069>

*   [简介](#introduction)
*   [示例场景](#example-scenario)
*   [环境设置](#environment-setup)
*   [开发流程](#development-flow)
*   [开始版本控制](#starting-version-control)
*   [部署到生产](#deploying-to-production)
*   [修改流程](#modifying-the-flow)
*   [更新产量](#updating-production)
*   [总结](#summary)

### 简介

关于 NiFi 最常见的问题之一是*“我如何部署我的流？”*。

NiFi 的一个核心特性是，您可以修改实时数据流，而不必执行传统的设计和部署步骤。结果，“部署流程”的想法并没有从一开始就融入到系统中。

随着时间的推移，出现了一些解决这个问题的尝试，比如将 flow.xml.gz 置于版本控制之下，或者编写模板部署的脚本，但是这些都有其自身的问题和困难。

为了解决这个问题，社区投入了大量的精力来构建对数据流版本控制的直接支持。这项工作的结果是创建了一个全新的 Apache NiFi 子项目，名为 *NiFi Registry* 。

NiFi Registry 是一个补充应用程序，它为共享资源的存储和管理提供了一个中心位置。第一个版本关注于存储和管理版本化的流，NiFi 的 1.5.0 版本增加了与注册中心实例交互的能力。

本文的其余部分将使用一个示例场景来展示如何使用这些新功能来解决部署问题。

### 示例场景

对于这个例子，我们将使用来自 [https://randomuser.me](https://randomuser.me) 的数据，这提供了一个很好的方法来从数据库中生成一些类似于 users 表的测试数据。

为了生成一些样本数据，我们可以运行下面的 curl 命令:

```
curl "https://randomuser.me/api/?format=csv&results=10&inc=name,email,registered&nat=us" > users-10.csv 
```

查看这些数据，我们应该会看到如下内容:

```
name.title,name.first,name.last,email,registered
ms,lily,sims,lily.sims@example.com,2010-10-13 04:37:29
ms,marion,elliott,marion.elliott@example.com,2009-06-14 16:40:00
... 
```

在这个例子中，假设我们有以下需求:

*   使用 NiFi 将这些数据接收到 Solr
*   将数据从 CSV 转换为 JSON
*   添加名+姓组合的全名字段
*   根据职位添加一个性别字段(即先生=男性)
*   根据环境摄取到不同的 Solr 集合

为了模拟这个场景，我们将为 DEV 和 PROD 设置一个带有集合的 Solr 实例，为 DEV 和 PROD 设置单独的 NiFi 实例，并设置一个 NiFi Registry 实例来在环境之间共享我们的流。

### 环境设置

下载工件

*   NII 1 . 5 . 0
*   [NiFi 注册表 0.1.0](https://nifi.apache.org/registry.html)

## - [Solr 7.2.1](https://lucene.apache.org/solr/downloads.html)

提取一个目录中的所有档案，并创建两个 NiFi
副本

```
nifi-1.5.0-1
nifi-1.5.0-2
nifi-registry-0.1.0
solr-7.2.1 
```

编辑*nifi-1 . 5 . 0-2/conf/nifi . properties*并设置不同的网络端口

```
nifi.web.http.port=7080 
```

为 NiFi 创建目录以接收来自
的文件

```
mkdir nifi-1.5.0-1/data
mkdir nifi-1.5.0-2/data 
```

开始一切

```
./solr-7.2.1/bin/solr start -c
./nifi-registry-0.1.0/bin/nifi-registry.sh start
./nifi-1.5.0-1/bin/nifi.sh start
./nifi-1.5.0-2/bin/nifi.sh start 
```

为开发和生产创建 Solr 集合

```
./solr-7.2.1/bin/solr create -c data-dev -n \_default
./solr-7.2.1/bin/solr create -c data-prod -n \_default 
```

验证 Solr 集合已成功创建

[![](img/84091c9f2d0983e8df4a800d53a76215.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EzxZ6qmU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/01-solr-collections.png)

所有应用程序现在都应该在以下位置启动并运行:

*   http://localhost:8983/Solr
*   [http://localhost:18080/nifi-registry](http://localhost:18080/nifi-registry)
*   [http://localhost:8080/NII/](http://localhost:8080/nifi/)(dev NII)
*   [http://localhost:7080/NII/](http://localhost:7080/nifi/)(prod NII)

### 开发流程

首先，下载下面的模板并导入到开发 NiFi 实例(端口 8080):

*   [Solr-Ingest-User-data . XML](https://gist.githubusercontent.com/bbende/3ab0c7a0a13f33253435fed57b8c8798/raw/9ab0453a2444e971d35168e99f5a38a04f1925f9/Solr-Ingest-User-Data.xml)

导入模板后，您应该能够实例化它，并以以下过程组结束:

[![](img/8c12270e96ce51274ab170a78f722ded.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bv7XeTco--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/02-nifi-dev-pg.png)

进入该流程组应显示以下流程:

[![](img/7f260414617e26d1892f8c25b1adaae7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zc-BNsg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/03-nifi-dev-flow.png)

注意:在实例化模板之后，您必须启用所有的控制器服务并启动所有的处理器来运行所有的东西。

为了处理数据，我们将使用一些基于记录的处理器，这意味着我们需要一个模式。我们将要使用的模式如下所示，它是在 AvroSchemaRegistry 控制器服务中定义的。

```
{
"type": "record",
"name": "user",
"fields": [
  { "name": "title", "aliases" : ["name.title"], "type": "string" },
  { "name": "first_name", "aliases" : ["name.first"], "type": "string" },
  { "name": "last_name", "aliases" : ["name.last"], "type": "string" },
  { "name": "full_name", "type": ["string", "null"] },
  { "name": "gender", "type": ["string", "null"] },
  { "name": "email", "type": "string" },
  { "name": "registered", "type": "string" }
]
} 
```

请注意，*性别*和*全名*是可空的，因为它们不是我们初始数据的一部分，我们将在以后填充它们。

我们不会查看每个处理器的配置，但这里会简要描述每个处理器的功能:

*   GetFile 正在获取文件。give NiFi 主目录下的数据
*   *UpdateAttribute* 添加一个值为“user”的“schema.name”属性
*   *ConvertRecord* 使用 CSV 阅读器和 JSON 编写器将 CSV 转换为 JSON
*   *UpdateRecord* 通过连接名和姓来填充全名字段
*   *LookupRecord* 通过将标题字段的值传递给查找服务来填充性别字段
*   *PutSolrContentStream* 向 Solr 发送数据

对于 Solr 处理器，集合属性被设置为引用变量的 *${solr.collection}* 。如果我们在画布的某个地方右击并选择“变量”，我们可以看到这个变量的定义。

[![](img/bbacb9b1f7d6c696220b02633c024825.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yyYjUC0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/04-nifi-dev-vars.png)

我们可以通过将前面的 users-10.csv 复制到开发 NiFi 的摄取目录来测试摄取一些数据:

```
cp users-10.csv nifi-1.5.0-1/data 
```

然后，我们可以验证数据是否被接收到 Solr 中的 *data-dev* 集合中:

[![](img/f3d521c52072447045e5b8c162ab8d86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---1TCptAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/05-solr-data-dev.png)

### 开始版本控制

既然我们的开发环境中一切正常，我们希望将这个流程部署到生产环境中。为了做到这一点，我们将使用 NiFi Registry 将我们的流置于版本控制之下。

首先，我们需要在 NiFi 注册实例中创建一个 bucket。存储桶是一种在注册表中组织项目的方式，并在安全运行时作为分配权限的机制。

单击注册表右上角的工具图标会将我们带到 bucket administration 面板，从这里我们可以创建一个名为“Solr Flows”的新 bucket。

[![](img/aa903f4777a4ec6f4ab82c3a07e65c73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a1Jrs41b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/06-nifi-reg-bucket.png)

回到我们的开发 NiFi(端口 8080)，我们需要告诉 NiFi 我们的注册中心实例。我们可以从右上角菜单下的控制器设置中选择 Registry Client 选项卡并添加一个新的客户端来完成此操作。

[![](img/24a5b0755d3b36b1461ba5b1eead388a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--apfs1lcE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/07-nifi-reg-clients.png)

一旦定义了一个注册客户端，当右键单击进程组时，将会出现一个“版本”菜单。

[![](img/2d320bd33fda555259ec041f00ef2d95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Ru2AE96--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/08-nifi-dev-start-vc.png)

选择“Start version control”将提示我们选择一个注册表实例，选择一个 bucket，并为我们的流命名和描述，以保存在注册表中。

[![](img/bf83190ce2ffe4590a3905705fb67640.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Al7xVAC8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/09-nifi-dev-save-v1.png)

单击 Save 将把这个流的第一个版本发送到我们的注册中心实例。我们还可以看到，我们的流程组处于版本控制之下，并且与注册中心的最新版本保持同步。

[![](img/cfe7deff36973f7c2dd86af594cd9685.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n8lzlOBU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/10-nifi-dev-under-vc.png)

刷新注册中心的主页，我们可以看到我们流程的版本历史。

[![](img/b7b2269a20913f5c36aa8f177366d879.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sct6cch4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/11-nifi-reg-items.png)

### 部署到生产

既然我们已经将我们的流置于版本控制之下并保存到注册中心，我们只需要将该流导入到我们的生产 NiFi(端口 7080)中。

按照上一节中的相同步骤，在我们的生产 NiFi 中定义注册中心客户机。

在定义了注册中心客户端之后，我们可以将一个流程组拖到画布上，并看到一个通过导入一个版本化的流来创建流程组的新选项。

[![](img/5353311fb460f844d9e5aec6561a55e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A1-YTR94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/12-nifi-prod-add-pg.png)

选择“Import”将提示我们选择要导入的注册表、存储桶、流和版本。

[![](img/9ff8fc96fde5ab04b3500fd2651ddf46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YjDjgtH6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/13-nifi-prod-import.png)

在选择了我们的流之后，我们现在在我们的生产环境中有了完全相同的流。

请记住，我们希望在生产中使用不同的 Solr 集合，因此我们需要编辑变量，以便 *${solr.collection}* 指向我们的生产集合(data-prod)。

[![](img/2d6cf3dc1f9cede276e7db3d93eec511.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ryn2pnAP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/14-nifi-prod-vars.png)

我们还必须启用所有的控制器服务，并启动所有的处理器来让流运行。

注意:编辑变量和启用/启动组件这两个操作只有在第一次导入时才是必要的，以便设置好一切。在将来的更新中，这些值将被保留。

一切运行正常后，我们现在可以将 users-10.csv 文件复制到生产 NiFi:
下的数据目录中

```
cp users-10.csv nifi-1.5.0-2/data/ 
```

检查 Solr 中的 data-prod 集合，我们现在可以看到数据被吸收到我们的生产集合中。

[![](img/5edcf2e6f2cee75b7bd40a0fcf54656b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c3q12cex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/15-solr-data-prod.png)

### 修改流程

在我们的流程运行一段时间后，我们可能会决定改变 Solr 处理器上处理错误的方式。所以我们回到我们的开发 NiFi，将 PutSolrContentStream 的失败关系连接到一个 LogAttribute 处理器，而不是自动终止。

[![](img/3a0ab4641d9e9656fc65993582241dcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rx5SMzf_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/16-nifi-dev-update-flow.png)

查看我们的流程组，我们现在可以看到一个不同的图标，表明已经进行了本地更改，我们的流程不再与注册中心的最新版本同步。

[![](img/45db0696c5141d9ebeb280531eb77db8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DaM94QKS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/17-nifi-dev-local-changes.png)

从“版本”菜单中，选择“显示本地更改”将显示自上一版本以来已更改的所有内容的列表。

[![](img/bedbcdcc3f5a5d45d2798ad308ce6a9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6DaFnRk9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/18-nifi-dev-local-changes-list.png)

当进行了本地更改后，我们可以恢复到上一个版本，或者提交以保存更改。在这种情况下，我们希望提交更改，以便将它们部署到生产环境中。

[![](img/fb0104ff126abc33577fcef25b892cff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--opAqUzcP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/19-nifi-dev-save-v2.png)

注册表现在显示我们的流有两个版本。

[![](img/19beba584bb52efaea50dedebce811c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EREhCvwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/20-nifi-reg-v2.png)

### 更新生产

从我们的产品 NiFi 中，我们现在可以看到一个更新版本的流程。

[![](img/59f6b272d1d219a3a648acfec611aba0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rzZSTi6q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/20-nifi-prod-upgrade-avail.png)

从版本菜单中，我们选择“更改版本”来升级到较新的版本。

[![](img/bf93c314d6f48651dde5b9bda04e0c71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hUKT6uLJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/21-nifi-prod-change-version.png)

更改到新版本后，我们看到新的 LogAttribute 处理器连接到 PutSolrContentStream。

[![](img/d5bc2a3387a988fbe5463f4ba08d4529.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EEbNlk59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bbende.github.io/asseimg/nifi-deploy-flow/22-nifi-prod-updated-flow.png)

请注意，LogAttribute 处理器需要启动，因为它是作为更新的一部分添加的，但是所有其他组件仍然在运行，并且之前为生产 Solr 集合所做的变量更改仍然存在。

### 总结

希望这很好地介绍了如何利用 NiFi 和 NiFi Registry 提供的新功能。

有关其他信息，请查看以下资源:

*   [NiFi 用户指南-对数据流进行版本控制](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html#versioning_dataflow)
*   [NiFi 注册用户指南](https://nifi.apache.org/docs/nifi-registry-docs/html/user-guide.html)