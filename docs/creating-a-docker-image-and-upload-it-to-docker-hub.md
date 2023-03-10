# 创建 Docker 映像并将其上传到 Docker Hub

> 原文：<https://dev.to/ech0server/creating-a-docker-image-and-upload-it-to-docker-hub>

## 创建 Docker 图像

我将使用一个 Docker 映像，以便能够在同一台机器上使用多个 github 帐户，而不会有太多麻烦。为了保持图像小，我将使用 Alpine 作为基础图像，只安装基本的:git，vim，openssh-client。

为了构建图像，我们需要创建一个名为 Dockerfile 的新文件，并编写如下内容:

```
FROM alpine:latest

LABEL maintainer="@ech0Server"

RUN apk update
RUN apk add git vim openssh-client 
```

Enter fullscreen mode Exit fullscreen mode

然后，构建 docker 镜像运行:

```
docker build -t username/imagename:latest . 
```

Enter fullscreen mode Exit fullscreen mode

这个命令不仅会构建图像，还会用-t 选项对其进行标记，在我的例子中我是这样做的:

```
docker build -t ech0server/multigitaccount:latest . 
```

Enter fullscreen mode Exit fullscreen mode

我将假设您已经有一个 docker hub 帐户或创建一个 Docker Hub 帐户，然后:

```
$ export DOCKER_ID_USER="username"
$ docker login #it will prompt you for your username and password 
```

Enter fullscreen mode Exit fullscreen mode

如果您遵循了前面的说明，您的图像应该已经被标记，如果没有，那么您可以通过:
来标记您的图像

```
docker tag my_image $DOCKER_ID_USER/my_image 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以将 Docker 映像推送到 Hub:

```
docker push ech0server/multigitaccount:latest 
```

Enter fullscreen mode Exit fullscreen mode

完成后，你可以到:[https://hub.docker.com/u/username](https://hub.docker.com/u/username)查看图片是否上传成功。

我的形象可以在这里找到:[https://hub.docker.com/r/ech0server/multigitaccount/](https://hub.docker.com/r/ech0server/multigitaccount/)

而且你可以通过做:
来拉动它

```
docker pull ech0server/multigitaccount 
```

Enter fullscreen mode Exit fullscreen mode