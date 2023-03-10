# 写了一个叫 container-up 的工具

> 原文：<https://dev.to/nasa9084/container-up-5823>

[![container-upというツールを書いた](img/195b880c79e7bd517eca9d6f7e4b7c67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tZqI2lWf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/conteimg/2017/12/container-up.png)

[写了名为 container-up](https://github.com/nasa9084/container-up) 的工具，特此介绍。

## Background

这个博客在名为[ghost](https://ghost.org/) 的博客引擎上运行。 作为动作环境使用了[Docker](https://www.docker.com/) ，使用了 Ghost 的[官方形象](https://hub.docker.com/_/ghost/)。

从过去的经过来看，一直在单个 Docker 容器上运行，永久数据是作为 Docker 卷装载的形式。

总之，如果使用的是 Docker Compose 或 Kubernetes 等定位工具，则容器的版本升级相对简单。

例如，使用 Docker Compose 时，可以在`docker-compose up`中替换为用新图像制作的容器。

但是，单独使用 Docker 时，基本上需要手动进行更换。

Ghost 容器更新的时候手动进行了蓝绿升级，但是 Ghost 的更新速度相当快，每次更换容器变得很麻烦。

为了让它变得轻松，制作了**container-up** 。

## 安装

### 有 Go 环境的人

已经有 Go 语言环境的人，可以通过以下命令使用。

```
$ go get github.com/nasa9084/container-up 
```

Enter fullscreen mode Exit fullscreen mode

### 其他人

没有 Go 语言环境的人，请从[Releases](https://github.com/nasa9084/container-up/releases) 页面按照自己的 OS 下载二进制文件，通过路径。

面向 windows、linux、macos，分别准备了 amd64 版的二进制文件。

只有 macos、linux(CentOS 7)进行动作确认。

除此之外环境的人，请在预先安装[dep](https://github.com/golang/dep) 的基础上使用以下命令进行编译。

```
$ git clonse https://github.com/nasa9084/container-up.git
$ cd container-up
$ dep ensure
$ go build -o container-up main.go 
```

Enter fullscreen mode Exit fullscreen mode

编译后，请将二进制文件移动到任意位置并通过路径。

## 使用方法

基本用法只是将容器名称或容器 ID 传递给参数。

```
$ container-up CONTAINER_NAME 
```

Enter fullscreen mode Exit fullscreen mode

使用与给定容器同名的映像创建并替换新容器，而不保留卷、网络等设置。
使用
`:latest`的图像时，在`docker pull`后执行该命令，可以更换为由最新的图像制作的容器。

原来的集装箱除了带`--rm`选项启动的情况以外，在带`_oldContainer`后缀的状态下停止。

如果有什么问题，放回这个集装箱就好了。

如果不需要原来的容器的情况下，加上`--rm`选项的话，在替换的时候会删除。

```
$ container-up --rm CONTAINER_NAME 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果您使用的是带有版本标记的映像，而不是`:latest`，并且想要使用新版本的映像，则也可以指定新的映像名称并运行。

```
$ container-up -i IMAGE_NAME CONTAINER_NAME 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，将使用指定的图像创建要替换的容器。

这些命令将继承卷的装载设置，但所有其他文件将替换为新映像。

如果在卷装载以外有想要继承的文件等的情况下<sup>[[1]](#fn1)</sup> 、`-f`通过在选项中指定文件路径，复制到新的容器中

```
$ container-up -f /path/to/file.ex CONTAINER_NAME 
```

Enter fullscreen mode Exit fullscreen mode

`-f`选项可以指定多个，想复制多个文件时请指定多次。

```
$ container-up -f /path/to/file1 -f /path/to/file2 CONTAINER_NAME 
```

Enter fullscreen mode Exit fullscreen mode

目前安装的功能如上所述。

帮助可以通过打开`-h`选项看到。

* * *

1.  例如设定文件等