# 使用 Docker 进行代码实验

> 原文：<https://dev.to/programliftoff/using-docker-for-experimenting-with-code-8i>

*这篇文章最初发表在[programmingliftoff.com](http://programmingliftoff.com/)上，名为[，使用 Docker 对代码](http://programmingliftoff.com/using-docker-for-experimenting-with-code/)进行实验。*

[![Docker](img/7d3173895243cf6be7e2c7df68b1af91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uJTmSlHh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bkhr8p5inu6p4qnlarv3.png)

学习编码的最好方法之一是通过编写或修改程序来看看它们能做什么。对我来说，这通常包括创建一个名为类似于`delete.py`的文件，并在其中编写一些代码来帮助我了解更多关于这种语言的知识。我的桌面上不可避免地会出现一堆名为`delete1.py`、`delete2.py`、`tmp.c`、`temp.rb`、`temp1.py`等等的文件。这些通常与一些我想保存在桌面上的图片和其他文件混在一起，所以删除它们就变成了一项半费时的任务。如果有更好的方法呢！？

## 使用 Docker 进行代码实验

最近我开始使用 docker 来完成这个任务。使用 Docker 的好处是，容器提供了一个与计算机隔离的层，因此在大多数情况下，您可以在容器中执行任何命令，而不用担心会在计算机上造成混乱。如果容器内部出现严重问题，不用担心！只需启动一个新的容器，然后再试一次！酷，那么我如何创建一个容器并安装我需要的编程语言和工具呢？很高兴你问了。在下一节中，我将解释如何开始使用 docker 文件，这样您就可以开始运行了！然后我将逐行分解我使用的 docker 文件。它看起来有点长，但是每一行都相当简单。

## 使用 Dockerfile 文件

docker 文件和支持文件可以从 GitHub 仓库下载，这里是: [docker-playground](https://github.com/programming-liftoff/docker-playground) 。

GitHub 页面上有一个 Readme 来帮助您开始，但是我也会在这里提供说明。

**入门**
先决条件:在开始之前，确保您已经安装了 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 和 [Docker](https://docs.docker.com/engine/installation/) 。
1)使用`git clone [https://github.com/programming-liftoff/docker-playground.git](https://github.com/programming-liftoff/docker-playground.git)`
克隆存储库 2)通过键入`cd docker-playground/`
将 cd 复制到目录中 3)通过键入`docker build -t playground:latest .`
构建映像 4)通过键入`docker run --rm -it playground`
在容器中启动 bash shell 就这样！现在你可以创建文件并运行它们，而不用担心弄乱你的桌面！只需键入`exit`或按`Ctrl-c`退出容器，不会留下任何文件的痕迹。

**补充说明**
在 Dockerfile 的开头，一些环境变量是用下面几行设置的:

```
ENV username andrew
ENV password pass
ENV rootpassword toor 
```

Enter fullscreen mode Exit fullscreen mode

当容器装载一个 shell 时，它将装载给用户`andrew`，密码设置为`pass`。根用户的密码设置为`toor`。

如果您希望更改这些值，只需在这个地方更改它们，然后用`docker build -t playground:latest .`重新构建映像来更新容器的设置。

## 文档文件

这里是我分解 does 文件和支持文件的地方，这样您可以了解每一行是做什么的，并修改代码以满足您的需要。我们先从最小的文件“entry_point.sh”开始。

#### entry_point.sh

该文件在容器每次启动时运行。这样一来，在这里放代码就类似于在‘放代码。“bash_profile”或“profile”等。

完整代码可以在这里查看:[入口点](https://github.com/programming-liftoff/docker-playground/blob/master/entry_point.sh)。

1)这段代码检查文件“rvm.sh”是否存在。如果存在，则执行该文件。这是将 Ruby 的可执行文件添加到 PATH 变量中所必需的。如果您现在想使用 Ruby，可以从“entry_point.sh”中删除它。

```
# For RVM - Ruby
if [ -f /etc/profile.d/rvm.sh ]
then source /etc/profile.d/rvm.sh
fi 
```

Enter fullscreen mode Exit fullscreen mode

2)这段代码启动一个 bash shell。如果您删除它，容器将启动和停止，而不给你一个外壳。随时可以试一试！

```
# Get a shell
/bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

#### Dockerfile

docker 文件包含大部分代码。它用于构建 docker 映像，容器可以从该映像启动。当我们探索它的时候抓紧了！

完整代码可以在这里找到: [Dockerfile](https://github.com/programming-liftoff/docker-playground/blob/master/Dockerfile) 。

1) Docker 映像可以从基础映像开始构建。在这种情况下，新映像将从基础映像中的所有内容开始，加上 Dockerfile 文件中添加的任何内容。这一行告诉 Docker 使用最新版本的 ubuntu 映像作为基础开始构建映像。

```
FROM ubuntu:latest 
```

Enter fullscreen mode Exit fullscreen mode

2)这一部分在前面已经展示过了。它设置了一些在构建映像时使用的环境变量。注释中有一个 TODO，让您知道您可以将它们设置为您喜欢的任何值。这些环境变量可以在 docker 文件中访问，也可以在构建容器后在容器中访问。

```
#==========================
# TODO: Use desired values
#==========================
ENV username andrew
ENV password pass
ENV rootpassword toor 
```

Enter fullscreen mode Exit fullscreen mode

3)这个部分为容器创建一个新用户。默认情况下，使用 ubuntu 镜像，容器将作为根用户启动。使用计算机作为根用户通常是不被允许的，因为这可能是危险的(当你拥有完全权限时，很容易把事情搞砸)。这可能没什么大不了的，因为我们正在使用一个容器，但是创建一个新用户与您通常使用命令行的方式相似。docker `RUN`命令运行一个 shell 命令来改变正在构建的容器中的某些内容。在这种情况下，如前所述，只需运行 bash 命令来添加用户，设置用户的密码，并授予用户“sudo”权限。

```
#============
# Add a user
# 1) First add a user and set user's shell to bash and create a directory for user in the /home folder
# 2) Next set the user's password
# 3) Then add the user to the `sudo` group so the user can use the `sudo` command
#============
RUN useradd -ms /bin/bash $username
RUN echo $username:$password | chpasswd
RUN adduser $username sudo 
```

Enter fullscreen mode Exit fullscreen mode

4)这一行更新容器的包管理器。这在使用 apt-get 安装包之前是必要的。

```
RUN apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

5)这些行不是必需的，但是它们可以防止显示一些警告。Docker 在不接受/允许用户输入的情况下构建容器，所以第一行告诉 debconf(Debian 配置管理系统)不要担心向用户显示对话框。第二个命令安装 apt-utils，以允许软件包配置在安装后直接进行，而不是延迟。

```
#=====================
# Updates for debconf
# Prevent message 'debconf: unable to initialize frontend: Dialog'
# Prevent message 'debconf: delaying package configuration, since apt-utils is not installed'
#=====================
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
RUN apt-get install -y --no-install-recommends apt-utils 
```

Enter fullscreen mode Exit fullscreen mode

6)这些行使用 apt-get 包管理器来安装一些基本的工具，这些工具在你的命令行上是可以得到的。其中包括`sudo`、`curl`(用于下载文件，在安装编程语言时很有用)、许多其他语言安装也需要的 c 和 c++编译器，以及`nano`，一个简单的文本编辑器。如果您使用另一个文本编辑器，如 emacs 或 vim，您可能希望在这里添加更多的安装命令。

```
#================
# Basic software
# build-essential Includes:
#  - gcc (C language compiler)
#  - g++ (C++ compiler)
#  - make
#================
RUN apt-get install -y sudo
RUN apt-get install -y curl
RUN apt-get install -y build-essential
RUN apt-get install -y nano 
```

Enter fullscreen mode Exit fullscreen mode

7)这部分安装 Python 3 和 Pip 3，Pip 3 是 Python 3 的包管理器。

```
#=========
# Python3
#=========
RUN apt-get install -y python3 && \
    apt-get install -y python3-pip 
```

Enter fullscreen mode Exit fullscreen mode

8)这一节安装 RVM，Ruby 版本管理器，它使得使用 Ruby 更加容易。I 附带了 ruby 语言解释器以及`irb`(交互式 ruby REPL)和一些附加软件。它首先下载 gpg 密钥来验证 RVM 安装是合法的。接下来，它下载 RVM 最新稳定版的 Ruby。然后，它将之前创建的用户添加到`rvm`组。

```
#================
# Ruby (via RVM)
#================
RUN gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB && \
    \curl -L https://get.rvm.io | bash -s stable --ruby && \
    adduser $username rvm 
```

Enter fullscreen mode Exit fullscreen mode

9)本节下载 Go 编程语言的 1.9.1 版本。首先，它使用 curl 下载压缩的语言文件。然后，它将文件解压缩到/usr/local 目录。然后它删除压缩文件。最后，它更新 PATH 环境变量以包含 Go 二进制文件的路径(例如，`go build`和其他 Go 命令)。

```
#====
# Go
#====
RUN curl -o ./go.linux-amd64.tar.gz https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go.linux-amd64.tar.gz && \
    rm -f go.linux-amd64.tar.gz
ENV PATH="${PATH}:/usr/local/go/bin" 
```

Enter fullscreen mode Exit fullscreen mode

10)此部分设置根用户的密码。它使用在 docker 文件开头设置的 rootpassword 环境变量作为 root 用户的新密码。

```
#===================
# Set root password
#===================
RUN echo root:$rootpassword | chpasswd 
```

Enter fullscreen mode Exit fullscreen mode

11)这些行将 entry_point.sh 文件从您的计算机复制到容器的根目录(/entry_point.sh)。第二行更改文件的权限，使其可执行。我们需要这样做，因为该文件是用它在您的计算机上的相同权限复制的。这意味着，如果它不能在你的电脑上执行，Docker 将无法执行它。您可以在您的计算机上将权限更改为所需的权限，以便用正确的权限复制它；然而，为了使它每次都能工作，这个 Dockerfile 并不假定文件具有正确的权限。第三行告诉 Docker 使用文件“/entry_point.sh”作为入口点。这意味着该文件将在容器每次启动时运行。

```
#================
# Set ENTRYPOINT
# Copy entry_point file and make it executable.
#=================
COPY ./entry_point.sh /
RUN chmod 744 /entry_point.sh
ENTRYPOINT ["/entry_point.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

12)这里的第一行更改了容器构建的当前用户。此后的所有命令都将以该用户身份执行。这也使得当容器启动时，它以这个用户的身份加载 bash shell。最后一个命令允许我们指定容器启动时 shell 所在的目录。这被设置为在用户的主目录中加载 bash shell。

```
#==================
# Set default user
# 1) Set the default user when the container starts
# 2) Set the default directory to load when container starts
#==================
USER $username
WORKDIR /home/$username 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样！

你成功了！恭喜你！现在，您不仅可以使用这个 docker 文件来创建图像和容器，还可以在 GitHub 上派生这个 docker 文件，并对其进行定制以完全满足您的需求！Docker 是一个强大的工具，这只是它的众多用途之一。学习是一步一步来的，所以掌握这些知识，然后继续前进，增加你对 Docker 能为你做什么的理解！感谢阅读！