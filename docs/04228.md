# 仙丹伞应用程序

> 原文：<https://dev.to/stefandorresteijn/dockerizing-elixir-umbrella-apps-6go>

*本文最初发布于 [Sjauf](https://sjauf.io/dockerizing-elixir-umbrella-apps-2d4c1c7a8335) 。*

在 [Sjauf](http://sjauf.nl) 时，我们希望找到一种方法来快速部署我们的任何应用程序，同时监控它们，只需几次点击就可以升级它们，并且无需了解我们服务器的所有细节就可以扩展它们。我们是一家小型创业公司，所以我们没有雇佣 DevOps 团队，甚至没有服务器管理员。我们的解决方案需要易于理解，同时保持稳健和安全。我们是这样做的。

**码头工人**

将应用程序部署到服务器可能是一项艰巨的任务。服务器与您的开发机器有不同的架构，往往运行在更差的规格上，并且每次启动时都需要从头开始配置。最重要的是，你的开发环境可能和你同事的不一样。这就是为什么我们使用 Docker 在容器中运行我们的应用程序，然后将它们部署到我们的服务器上。因为我们的 Docker 容器总是在相同的架构下运行，只有我们告诉它安装的工具，所以我们知道它们在我们的服务器和开发机器上会表现相同。这解决了我们的许多问题，但是为 Elixir umbrella 应用程序创建一个合适的 docker 文件和环境并不像看起来那么容易。

**某事某事，问题问题**

当我们想要创建 docker 容器时，我们遇到了一些问题。我们在 Mac OSX 电脑上构建我们的应用程序，然后在我们的容器中开始构建。因为我们的 Mac 架构与 docker 容器架构略有不同，所以应用程序无法运行。这意味着必须从 docker 容器本身构建我们的应用程序。我们遇到的另一个问题是在我们的 Postgres 服务器还没有运行的时候启动我们的应用程序。在一个单独的容器中运行 Postgres 数据库并不少见(而且我认为这实际上更好)。使用 docker-compose，您可以立即启动整个应用程序——数据库和应用程序。不幸的是，当您在启动数据库之前启动 Elixir 应用程序时，Ecto 会报错，并且您的应用程序无法启动。这就是我们添加 postgres 等待脚本的原因，我们将它作为启动命令运行。看起来有点像这样:

```
#!/bin/bash

# wait-for-postgres.sh

set -e

host=$1
username=$2
password=$3
db=$4
start_elixir=$5

until PGPASSWORD="$password" psql -h "$host" -U "$username" -d "$db" -c '\l'; do

    >&2 echo "Postgres is unavailable - sleeping"
    sleep 1

done

>&2 echo "Postgres is up - executing command"

exec $start_elixir 
```

Enter fullscreen mode Exit fullscreen mode

**Dockerfile**

要创建可以在容器中运行的 docker 映像，您需要一个 docker 文件。下面是我们的 docker 文件最后的样子:

```
 FROM ubuntu:14.04.3

# Set the locale, otherwise elixir will complain later on
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# wget for convenience, use curl if you want to
# curl for Appsignal
# postgresql-9.6 for checking if the server is running yet so we can launch our applications when PG is already up
RUN apt-get -y -q install wget curl postgresql postgresql-contrib

# add erlang otp
RUN wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
RUN dpkg -i erlang-solutions_1.0_all.deb

# Update apt libraries
RUN apt-get update

# Install build essential for Appsignal
RUN apt-get install -y build-essential

# Install erlang and elixir
RUN apt-get install -y -q esl-erlang elixir

# Create app folder
ADD . /app

# Copy wait-for-postgres script. We need this script to check up on Postgres and wait for it to be ready
COPY scripts/wait-for-postgres.sh /app/wait-for-postgres.sh

# Own wait-for-postgres script so we can run it
RUN chmod +x /app/wait-for-postgres.sh

# Set app folder to work dir
WORKDIR /app

# Installs hex locally
RUN mix local.hex --force

# Installs rebar locally
RUN mix local.rebar --force

# Clean appsignal. If we don’t do this, it can’t compile on a second build
RUN mix deps.clean appsignal

# Installs all dependencies for production
RUN mix deps.get --only-prod

# Compiles application
RUN MIX_ENV=prod mix do compile, release --env=prod

# Set Port
ENV PORT 8080

# Run App
CMD [“_build/prod/rel/sjauf_umbrella/bin/sjauf_umbrella”, “foreground”] 
```

Enter fullscreen mode Exit fullscreen mode

does 文件中的注释应该让您了解每一行的作用。因为我们的子应用程序是作为 git 子模块添加到我们的存储库中的，所以它们总是最新的。这些命令确保它们都是自动编译和发布的。构建应用程序现在就像运行 docker build 命令一样简单:

```
docker build -t sjauf/sjauf_umbrella . 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们的组织名是 Sjauf，我们的存储库名为 sjauf_umbrella。请确保您正确地命名了您的存储库，以便当您将来回顾这个应用程序时，可以记住您正在处理哪个应用程序，坐在您在迈阿密的顶层公寓的屋顶上，与迈阿密海豚啦啦队一起享用一整瓶 black label。

我希望这篇文章能帮助你们轻松地设置自己的 Elixir Umbrella 应用程序。我们 DevOps 滑稽动作的更多文章将很快跟上！