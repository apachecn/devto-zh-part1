# Docker、Glide 和 Fresh 的热重装 Go 程序

> 原文：<https://dev.to/craigchilds94/hot-reloading-go-programswith-docker-glide--fresh>

# 简介

开发 Go 应用程序可能是一项艰苦的工作，特别是如果您没有使用 IDE(出于偏好)，并且您正在将 Go 程序作为一项服务集成到一个更大的生态系统中，并且在开发期间必须在本地运行。例如，与大型 web 应用程序一起运行的队列工作器，作为 Docker 容器运行。每次我们修改代码时，我们都必须终止 Docker 容器，重新构建 Go 二进制文件，并重新运行 Docker 容器。这是低效的，并且很容易自动化。我们开始吧，好吗？

# 先决条件

我假设你知道围棋程序如何工作以及如何开发它们的基本知识。我还假设你知道 Docker & Containers 是什么，如果不知道，我建议你[仔细阅读它们](https://www.docker.com/what-docker)，或者试着挑选出这篇文章中与你的需求相关的部分。

# 设置新鲜的&滑翔

我们在这里面临的现实问题是，当我们更改一行代码时，我们不应该被留下来手动重启容器，甚至做一些事情。这将是乏味和浪费这么多时间。我们需要能够在文件改变时动态重建二进制文件的东西。我们自己开发一个简单的观察器是可能的，但是这是时间问题，其他人已经为我们做了。所以我欢迎你，[新鲜的](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpilu%2Ffresh)。

Fresh 是一个 Go 程序，你可以在 Docker 容器内外运行它，它会监视文件，重新构建你的二进制文件并执行它。它有一个简单的配置文件，您可以将它包含在您的项目文件夹中，这样您就可以忽略目录等。

默认情况下，它会查找一个名为 runner.conf 的文件，该文件如下所示:

```
root:  .  tmp_path:  ./tmp  build_name:  runner-build  build_log:  runner-build-errors.log  valid_ext:  .go,  .tpl,  .tmpl,  .html  no_rebuild_ext:  .tpl,  .tmpl,  .html  ignored:  assets,  tmp  build_delay:  600  colors:  1  log_color_main:  cyan  log_color_build:  yellow  log_color_runner:  green  log_color_watcher:  magenta  log_color_app: 
```

新鲜真的很好上手，只要`go get github.com/pilu/fresh`跑新鲜。它在你的终端上提供了一个很好的一步一步的打印输出，告诉你它在做什么，它看到了哪些文件发生了变化。

> 它工作得很好，当然也做了我们需要它做的事情。

我对它唯一的不满是，它似乎不能接收新文件；虽然简单地保存或“触摸”一个现有的文件将触发重建。虽然这可能是由于我的配置造成的，但我还没有花足够的时间来查找原因。

现在又来了[滑翔](https://medium.com/r/?url=https%3A%2F%2Fglide.sh%2F)，`go get`类固醇。这是我所有 Go 程序的 goto 包管理器。这个工具允许我们指定程序的依赖关系，并轻松地将它们拉进来。这使得在一个项目上协作和锁定包的版本变得更加容易。我强烈推荐这个工具给任何想要认真对待它的人。

> 本质上，它是一个漂亮的“go get”包装，带有配置文件、版本锁定和缓存。你为什么不用它？

由于 Fresh 的限制，它一次只能查看这么多文件，所以我们不得不忽略我们的 Glide 供应商文件夹。**boooooooo**。不幸的是，这意味着每当我们更新一个依赖项时，我们都必须手动重新启动。尽管一个简单的解决方法是运行`glide update && touch main.go`这样，一旦我们更新了我们的 Glide 依赖项，我们将触发 Fresh 的观察者重新构建，对吗？

只需在配置中将 vendor 添加到“ignored key”中，就像这样(这样可以避免抱怨有太多文件需要查看):

```
ignored: assets, tmp, vendor 
```

到目前为止，如果您在 Docker 生态系统之外工作，这种设置会工作得非常出色，所以接下来我们将把它集成到一个简单的 Docker 文件解决方案中，您可以与其他 Docker 容器一起运行。

# 设置停靠栏

如果你还没有安装和配置 Docker，那么请这样做。

我们不需要对我们在这里做的事情做太多的改变，而是直接在终端中运行命令；我们将基于官方的`golang:alpine`图像构建一个 Docker 图像，它将为我们处理这些。留下我们需要手动运行的唯一命令是`glide update && touch main.go`和典型的“Docker run ”,当我们添加/删除/更新 Glide 依赖项时，不需要我们自己重新构建二进制文件或容器。

创建一个包含以下内容的 Dockerfile 文件:

```
# create image from the official Go image
FROM golang:alpine

RUN apk add --update tzdata \
    bash wget curl git;

# Create binary directory, install glide and fresh
RUN mkdir -p $$GOPATH/bin && \
    curl https://glide.sh/get | sh && \
    go get github.com/pilu/fresh

# define work directory
ADD . /go/src/project_folder
WORKDIR /go/src/project_folder

# serve the app
CMD glide update && fresh -c runner.conf main.go 
```

请记住将“project_folder”替换为您正在处理的项目的文件夹名称。为了让 Glide 在 Docker 映像之外正常工作，你的项目必须在你的`$GOPATH`中，就像它在 Docker 文件中一样，所以`$GOPATH/src/project_folder`

为了启动这个容器，我们可以在我们的项目根目录下运行以下命令(无论 Dockerfile & main.go 文件在哪里):

```
docker run -it --volume=$(PWD):/go/src/project_folder --name=my_project_name image_name 
```

如果我们在 Docker 网络中与其他容器一起运行它，我们需要将网络标志添加到该命令中，因此如果您有一个名为 my_app 的网络，它将是:

```
docker run -it --volume=$(PWD):/go/src/project_folder --name=my_project_name --network=my_app image_name 
```

注意，我们需要从我们的工作目录到我们的容器文件夹中的文件夹创建一个卷，这将允许我们在容器中运行的观察器拾取我们本地文件系统中的更改。

这应该是您开始编写棒极了的热重装 Go 程序所需要的一切，这些程序带有在 Docker 容器上运行的托管依赖项。*高五*

注意:我不建议您将这种设置用于生产应用程序，这仅仅是一种在开发时消除重新构建代码所需时间的简洁方式。

祝你好运，玩得开心！

这是[https://medium . com/@ Craig childs 94/hot-reloading-go-programs-with-docker-glide-fresh-D5 f1 ACB 63 f 72](https://medium.com/@craigchilds94/hot-reloading-go-programs-with-docker-glide-fresh-d5f1acb63f72)的转帖