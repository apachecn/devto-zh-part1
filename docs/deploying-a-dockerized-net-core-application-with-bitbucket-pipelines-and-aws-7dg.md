# 部署一个 Dockerized。具有位桶管道和 AWS 的. NET 核心应用程序

> 原文：<https://dev.to/donbavand/deploying-a-dockerized-net-core-application-with-bitbucket-pipelines-and-aws-7dg>

这篇文章最初发表在我的博客网站上

# 简介:

我最近开始重构我的副业项目，重构包括转换我的。NET 4.5 Web Api 转换成. NET Core 2.0 Web Api，我希望它在 Docker 容器中的 AWS Linux 实例上运行。在花了太多时间构建代码并将其放入 AWS 上的 Docker 容器后，我决定需要一个持续交付部署管道。

有许多工具可以帮助您实现 CI/CD，我的项目存储在 Bitbucket 帐户中，我注意到去年“2016 年 10 月”Bitbucket 推出了一个名为 pipelines 的新功能。

Bitbucket pipelines 提供了一种内置于 Bitbucket 中的持续集成或交付服务。使用 Bitbucket 管道使我很容易创建一种方法来构建我的。NET 核心应用程序，并构建、标记和推送我的 Docker 映像到我的 AWS ECR 存储库。然后，我可以创建一个新的任务定义，并在 AWS 中更新我的服务，以利用这个新任务。当我将变更合并到我的主分支中时，构建管道就开始了。

这篇博文将向你展示我是如何设置我的 Bitbucket 管道的，它构建了我的。NET 核心应用程序，然后构建、标记和推送我的 docker 映像到我的 AWS ECR 存储库，以及我如何更新任务定义和服务。

# 什么是比特桶流水线

Bitbucket pipelines 允许人们构建、测试和部署应用程序，而无需设置昂贵且耗时的 CI/CD 服务器。Bitbucket 使用 docker 映像作为基础，建立一个允许您运行其他命令的环境。

Bitbucket 管道向您的项目添加了一个 bitbucket-pipelines.yml 文件，该文件在管道启动时使用，它保存了关于管道要做什么的所有命令。构建管道启动时运行的默认映像是 atlassian/default-image:latest，这允许您运行 linux 命令，如“sudo apt-get update”。只需在 pipeline bit bucket-pipelines . yml 文件中添加选项“docker: true ”,您的管道现在将拥有 docker 支持，并允许您运行 docker 命令。

当我按下 C#键时。NET 核心项目到我的 Bitbucket 帐户，Bitbucket pipelines 注册了我的代码变更，并启动了构建管道过程。

我喜欢这样的事实，我不必在多个应用程序之间切换来构建和部署我的代码，有了 Bitbucket 管道，我可以将我的代码推送到我的主分支，就是这样。在很短的时间内，代码将在我的 AWS EC2 实例上构建和更新。

# 我用 Bitbucket 管道做什么？

我的项目是一个运行在 AWS 上的 Linux Docker 容器上的. NET Core 2.0 应用程序。我的构建管道的第一部分包括以下内容

*   下载并安装。NET Core SDK
*   恢复并发布我的。网络核心应用
*   下载并安装 AWS SDK
*   登录 AWS
*   建立我的码头工人形象
*   标记我的 Docker 图像
*   将我的 Docker 映像上传到我的 AWS ECR 存储库中
*   在 AWS 中注册新的任务定义
*   更新我的 AWS 服务，以使用新的任务定义。

我在管道脚本中做了很多工作，正如你很可能看到的，下载并安装 AWS 和。NET Core SDK 每次运行管道都是费时又浪费。构建我的 Bitbucket 管道的第 2 阶段包括创建包含 AWS SDK 的 docker 映像，还包括。NET 核心构建，恢复和发布过程中我的 docker 文件。与其向你展示最终结果，我想先展示一下我是如何完成所有这些任务的，因为这对其他人可能是有用的。

# BitBucket 流水线代码-阶段 1

让我们看一下 bitbucket-pipelines.yml 文件，了解每个命令在做什么。

这里有必要提一下，bitbucket-pipelines.yml 文件对间距和所有内容的位置非常挑剔，所以要确保所有内容都正确对齐。

```
image: atlassian/default-image:latest

options:
  docker: true

pipelines:
  branches:
    master:
      - step:
          script:
          #.NET Core         
          # Register the Microsoft Product key as trusted.
          - curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
          - sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg

          # Set up host package feed.
          - sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'

          # Install .NET Core SDK.
          - sudo apt-get install apt-transport-https
          - sudo apt-get update
          - sudo apt-get -y install dotnet-sdk-2.0.0

          # Restore, Publish and Release the .NET core project
          - dotnet restore ./projectName.sln && dotnet publish ./projectName.sln -c Release -o ../obj/Docker/publish

          # AWS SDK

          # Download AWS SDK
          - curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"

          # Unzip the AWS SDK and Install
          - sudo apt-get install jq
          - unzip awscli-bundle.zip
          - ./awscli-bundle/install -b ~/bin/aws
          - export PATH=~/bin:$PATH

          # Login to AWS
          - export LOGIN=$(aws ecr get-login)
          - $LOGIN

          # Build my docker image
          - docker build -t projectName .

          # Tag and push my docker image to ECR
          - docker tag projectName:latest xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest
          - docker push  xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest

          # Register the ECS task definition and capture the version
          - export IMAGE_NAME=xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest
          - export TASK_VERSION=$(aws ecs register-task-definition --family ECRFamilyName --container-definitions "[{\"name\":\"ExampleName\",\"image\":\"$IMAGE_NAME\",\"portMappings\":[{\"containerPort\":80,\"hostPort\":80,\"protocol\":\"tcp\"}],\"memory\":128,\"essential\":true}]" | jq --raw-output '.taskDefinition.revision')

          # Set ECS service to desired count 0  
          - aws ecs update-service --cluster default --service ecrServiceName--desired-count 0

          # Set ECS service to desired count 1 and assign the new task-definition 
          - aws ecs update-service --cluster default --service ecrServiceName --task-definition ECRBonecrusher:$TASK_VERSION --desired-count 1 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细看看每一行代码，看看它们在做什么

首先，我告诉构建管道我想在构建中使用什么样的映像，同时我也启用了 docker 支持。

```
image: atlassian/default-image:latest

options:
  docker: true 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 Bitbucket 管道在不同的分支上运行不同的构建。这里我指定我想在我的主分支上运行下面的脚本。

```
pipelines:
  branches:
    master:
      - step:
          script: 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们安装。NET Core SDK，这样我们就可以构建我们的项目了。为了下载。NET Core SDK，我们需要注册 microsoft 产品密钥并设置主机软件包提要。如果不这样做，就无法安装 dotnet-sdk.20.0

```
 #.NET Core         
          # Register the Microsoft Product key as trusted.
          - curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
          - sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg

          # Set up host package feed.
          - sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'

          # Install .NET Core SDK.
          - sudo apt-get install apt-transport-https
          - sudo apt-get update
          - sudo apt-get -y install dotnet-sdk-2.0.0 
```

Enter fullscreen mode Exit fullscreen mode

接下来我恢复，出版和发布我的。网络核心项目

```
 # Restore, Publish and Release the .NET core project
          - dotnet restore ./projectName.sln && dotnet publish ./projectName.sln -c Release -o ../obj/Docker/publish 
```

Enter fullscreen mode Exit fullscreen mode

下一步是下载并安装 AWS SDK。

```
 # AWS SDK          
          # Download AWS SDK
          - curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"

          # Unzip the AWS SDK and Install
          - sudo apt-get install jq
          - unzip awscli-bundle.zip
          - ./awscli-bundle/install -b ~/bin/aws
          - export PATH=~/bin:$PATH 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们登录 AWS，这样我们可以推进到我的项目所在的 ECR 存储库。

```
 # Login to AWS
          - export LOGIN=$(aws ecr get-login)
          - $LOGIN 
```

Enter fullscreen mode Exit fullscreen mode

接下来的步骤是 Docker 映像的标准构建、标记和推送。

```
 # Build my docker image
          - docker build -t projectName .

          # Tag and push my docker image to ECR
          - docker tag projectName:latest xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest
          - docker push  xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了新的 Docker 映像，我们希望注册一个新的任务定义，以便在我们的服务中使用。

```
 # Register the ECS task definition and capture the version
          - export 
IMAGE_NAME=xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/projectName:latest
          - export TASK_VERSION=$(aws ecs register-task-definition --family ECRFamilyName --container-definitions "[{\"name\":\"ExampleName\",\"image\":\"$IMAGE_NAME\",\"portMappings\":[{\"containerPort\":80,\"hostPort\":80,\"protocol\":\"tcp\"}],\"memory\":128,\"essential\":true}]" | jq --raw-output '.taskDefinition.revision') 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是用我们的新任务更新我们的服务，关闭服务并将其重新启动，以利用新的任务定义。

```
 # Set ECS service to desired count 0  
          - aws ecs update-service --cluster default --service ecrServiceName --desired-count 0

          # Set ECS service to desired count 1 and assign the new task-definition 
          - aws ecs update-service --cluster default --service ecrServiceName --task-definition ECRTaskName:$TASK_VERSION --desired-count 1 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**
为了让我能够将我的服务降速到 0，然后更新服务上的任务定义，然后重新启动它，我已经在我的 AWS 服务上将“健康主机的数量”设置为 0%，这样我就不需要运行多个实例(节省成本)如果这是一个生产实例，那么我将运行多个实例，释放到一个实例，然后从其他实例排出连接并释放到它们。

我也将使用 ALB，但是因为这不是一个生产应用程序(还没有),所以短暂的中断是可以的。

# 比特桶流水线代码-阶段 2

我已经删除了 bitbucket-pipelines.yml 文件中许多不需要的代码，方法是将 dotnet 构建和发布步骤合并到我的 docker 文件中，然后将发布的应用程序步骤复制到运行时映像中，所有这些都在同一个 docker 文件中。

".NET Core Docker 映像现在可以使用 Docker 的一个新特性进行多阶段构建，该特性允许在一个 Docker 文件中使用多个 from 行。多阶段构建功能最近被引入 Docker 客户端稳定通道。使用此功能，您可以在 SDK 映像(也称为“构建映像”)中构建. NET 核心应用程序，然后将发布的应用程序复制到运行时映像中，所有这些都在同一个 docker 文件中。要查看这方面的实践示例，请查看。净码头样本报告。”

此外，通过创建包含 AWS SDK 的 docker 映像，我可以消除将其作为我的管道的一部分进行安装的需要。

下面是我的最终版本。

```
image: aaithal/aws-sdk

options:
  docker: true

pipelines:
  branches:
    master:
      - step:
          script:          
          # Login to AWS
          - export LOGIN=$(aws ecr get-login)
          - $LOGIN

          # Build my docker image
          - docker build -t imageName .

          # Tag and push my docker image to ECR
          - docker tag imageName:latest xxxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/imageName:latest
          - docker push xxxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/imageName:latest

          # Register the ECS task definition and capture the version
          - export IMAGE_NAME=xxxxxxxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/imageName:latest
          - export TASK_VERSION=$(aws ecs register-task-definition --family ECRName --container-definitions "[{\"name\":\"xxxxxxx-xxxxx-xxx-xxxx-xxxxxxxxxxxxx\",\"image\":\"$IMAGE_NAME\",\"portMappings\":[{\"containerPort\":80,\"hostPort\":80,\"protocol\":\"tcp\"}],\"memory\":128,\"essential\":true}]" | jq --raw-output '.taskDefinition.revision')

          # Set ECS service to desired count 0  
          - aws ecs update-service --cluster default --service serviceName --desired-count 0

          # Set ECS service to desired count 1 and assign the new task-definition 
          - aws ecs update-service --cluster default --service serviceName --task-definition taskDefintionName:$TASK_VERSION --desired-count 1 
```

Enter fullscreen mode Exit fullscreen mode

# 总结:

有许多工具可以用来实现 CI/CD，这是其中之一，对于一个附带的项目，我发现使用 Bitbucket 管道很容易使用，它与我的源代码在同一个地方。Bitbucket 管道也非常便宜。

能够将我的代码推向 master，然后忘记它，使开发我的项目变得容易得多，学习和研究我以前没有使用过的其他 CI/CD 工具也很有趣。

如果您有任何问题、意见或建议，我希望收到您的来信，请随时发送电子邮件或通过 twitter 联系。