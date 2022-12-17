# Makefile + Dockerfile 提高您的生产力

> 原文：<https://dev.to/earvin/makefile--dockerfile-to-boost--your-productivity-28b>

在使用 Docker 几个月后，我开始寻找自动化的方法，或者至少加快检查以下内容的常规程序:

*   `container is up`，
*   寻找容器的名称，
*   等等..

我曾经在 C/C++项目中使用过 Makefiles，现在完全忘记了。然后我看到 TJ 构建了他的 React 组件，他的 Go 项目和他用它们做的其他事情。

Simon Dittlman 在 Dockerfiles 和 Makefiles 上的文章通过自动化几个常用命令，很快说服了他。

以下是我使用的:

```
PORTS = -p 35729:35729 -p 4000:80

ENV = \

    -e VERSION=latest

VOLUMES = \

  -v pwd:data
```

```
include env_make
```

NS = earvin/博客版？=最新

REPO = earvin 名称= earvin 实例=默认值

。假的:构建推送 shell 运行开始停止日志 rm 发布开发

deps: gem 更新–no-ri–no-rdoc & & gem 安装 Jekyll pygments . Rb rdiscount rouge–no-ri–no-rdoc

build:docker build-t $(NS)/$(REPO):$(版本)。

推入:坞站推入$(NS)/$(REPO):$(版本)

shell:docker run–RM–NAME $(NAME)-$(INSTANCE)-I-t $(PORTS)$(ENV)$(NS)/$(REPO):$(VERSION)/bin/bash

run:docker run–RM–NAME $(名称)-$(实例)$(端口)$(环境)$(NS)/$(报告):$(版本)

start:docker run-d–label = Jekyll–NAME $(名称)-$(实例)$(端口)$(环境)$(NS)/$(回购):$(版本)

stop: docker stop $(名称)-$(实例)

RM:RM-f $(名称)-(实例)坞站

发布版本:build make push -e 版本=$(版本)

dev:Jekyll serve–watch–source = blog–incremental

日志:docker 日志$(名称)-(实例)

默认:构建

跑步:

*   将构建您的 docker 容器，并以守护模式启动它。
*   也会构建你的 docker 容器并运行它
*   `make logs`将显示日志
*   `make release`将构建 Docker 图像并将其发布到注册表

生成文件是可扩展的。新规则很容易定义:

#### **让宋承宪**:

进入正在运行的 Docker 容器

```
docker exec -i -t $(NAME)-$(INSTANCE) sh 
```

Enter fullscreen mode Exit fullscreen mode

NB: <sub>sh 可以被替换为或 bash 或/bin/bash，这取决于您的映像中是否安装了 bash。我的博客运行在基于 Alpine 的 Docker Image 上:我卸载了 bash git g++ openssh 来减轻它的重量。</sub>

#### **做试验**:

测试 Docker 容器。我通过运行`make build start test`来测试我的 Docker 注册表。为了测试我的 Docker 注册表，我从 Docker Hub 中取出一个 Alpine，并将其放入我的本地注册表中

```
// USER and PASSWORD are either exported before running
  //make ssh or defined in the env_make file

  docker pull alpine
  docker tag alpine localhost:5000/alpine
  docker login -u $(USER) -p $(PASSWORD) localhost:5000/alpine
  docker push localhost:5000/alpine 
```

Enter fullscreen mode Exit fullscreen mode

### 来源:

*   [Simon Dittlmann 关于如何使用 Makefile 加快 Docker 工作流程的博文](http://www.itnotes.de/docker/development/tools/2014/08/31/speed-up-your-docker-workflow-with-a-makefile/)
*   [TJ 关于用 NPM 最小化样板文件& Makefiles](https://medium.com/@tjholowaychuk/minimizing-boilerplate-with-npm-makefiles-3cfdce2934e7#.t2yeyure4)