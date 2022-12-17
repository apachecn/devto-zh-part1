# Django 到 Phoenix -第 3 部分:初始 Phoenix 设置

> 原文：<https://dev.to/michaeljones/django-to-phoenix---part-3-initial-phoenix-setup-4ma>

## 背景

我有一个用 Django 建立的网站，我想开始在[凤凰城](http://phoenixframework.org/)写它的一部分。

*   [第 0 部分:背景](https://dev.to/michaeljones/django-to-phoenix-part-0-6ij)
*   [第 1 部分:开发对接器](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)
*   [第 2 部分:ginx](https://dev.to/michaeljones/django-to-phoenix---part-2-nginx-9kg)

## 起点

在上一节中，我们将 [nginx](https://www.nginx.com/resources/wiki/) 添加到我们的 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 中，这样我们就可以将 Phoenix 引入到我们的容器中，并使用 nginx 根据 URL 路径在 Phoenix & Django 之间拆分传入的请求。

我们的下一步是建立一个基本的 Phoenix，并通过 Docker 容器访问它。这需要几个步骤:

*   安装[仙丹](https://elixir-lang.org/) &凤凰
*   创建一个凤凰项目
*   将仙丹和凤凰添加到我们的 Docker 图像
*   为我们的 Phoenix 应用程序添加一个 nginx 配置部分
*   让我们的 Phoenix 应用程序在容器中运行

## 安装仙丹&凤凰

为了创建一个新的 Phoenix 项目并在我们的开发机器上与之交互，我们需要安装 Elixir & Phoenix。我们不打算在这里深入讨论它，但是按照相关的安装指南([药剂](https://elixir-lang.org/install.html) & [凤凰](https://hexdocs.pm/phoenix/installation.html#content))应该可以让你设置好。

## 创建凤凰计划

Phoenix 附带了一个[混合任务](https://hexdocs.pm/phoenix/phoenix_mix_tasks.html#phoenix-specific-mix-tasks)来创建一个新的 Phoenix 项目。 [Mix](https://hexdocs.pm/mix/Mix.html) 是长生不老药生态系统的构建工具&任务运行器。因此，像创建项目、添加模块或运行测试这样的普通工作通常会有混合任务来帮助你完成。

在这种情况下，我们对创建新项目的`mix phx.new`感兴趣。我们希望 Phoenix 项目位于 Django 项目代码旁边的目录中。对我来说幸运的是，Django 代码已经在一个`dancetimetable`文件夹中，而不是直接在项目根目录中，所以我们也可以在项目根目录中一个名为`timetable`的文件夹中创建 Phoenix 项目。

为此，我们将运行以下命令:

```
mix phx.new timetable --no-brunch 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`--no-brunch`选项，因为我们的 Django 项目已经使用 [Webpack](https://webpack.js.org/) 来编译客户端应用程序，所以我们不需要 [Brunch](http://brunch.io/) 。

该命令询问我们是否想要“获取并安装依赖项”,我们回答“是”,因为我们想要。一旦它完成了安装和编译，它会打印以下有用的消息:

```
We are all set! Go into your application by running:

    $ cd timetable

Then configure your database in config/dev.exs and run:

    $ mix ecto.create

Start your Phoenix app with:

    $ mix phx.server

You can also run your app inside IEx (Interactive Elixir) as:

    $ iex -S mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

但是我们需要为我们的设置改变这些步骤。`mix ecto.create`命令负责为 Phoenix 项目创建 [Postgresql](https://www.postgresql.org/) 数据库，但是我们希望 Phoenix 使用 Django 应用程序已经存在的数据库。所以我们不打算运行`mix ecto.create`，而是编辑`config/dev.exs`来引用我们的开发数据库。变化如下:

```
+config :timetable, Timetable.Repo,
   adapter: Ecto.Adapters.Postgres,
   username: "postgres",
   password: "postgres",
-  database: "testing_dev",
-  hostname: "localhost", +  database: "tangotimetable",
+  hostname: System.get_env("DOCKER_HOST") || "localhost",
   pool_size: 10 
```

Enter fullscreen mode Exit fullscreen mode

为了让 Django 服务器从 Docker 容器内部与 Postgres 数据库对话，我们添加了与我们在[之前的帖子](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)中讨论过的相同的`DOCKER_HOST`查找。

有了这些，我们就可以运行`mix phx.server`。运行后，我们可以在浏览器中访问`http://localhost:4000`,并看到默认的 Phoenix 新项目登录页面。

这篇文章的其余部分是关于再次看到那个页面，但是是从 Docker 容器内部提供的。

## 给我们的 Docker 图像添加仙丹&凤凰

为了将 Elixir & Phoenix 安装到我们的 Docker 映像中，我们使用了[Marcelo CG/Phoenix](https://hub.docker.com/r/marcelocg/phoenix/~/dockerfile/)Docker 文件作为参考。经过一些实验后，我们的 docker 文件的开始看起来像这样:

```
 from ubuntu:14.04

run apt-get update -y
run apt-get upgrade -y

# (1) Updated - Add locales & inotify-tools
run apt-get install -y git python python-pip make g++ wget curl libxml2-dev \
        libxslt1-dev libjpeg-dev libenchant-dev libffi-dev supervisor \
        libpq-dev python-dev realpath nginx apt-transport-https locales \
        inotify-tools

# (2) Set locale information
run locale-gen en_US.UTF-8
env LANG en_US.UTF-8
env LANGUAGE en_US:en
env LC_ALL en_US.UTF-8

# Install yarn as before (unchanged)
run curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

# (3) Install erlang apt repository
run wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb \
    && dpkg -i erlang-solutions_1.0_all.deb

# (4) Update apt information & install erlang & elixir
run apt-get update -y
run apt-get install -y yarn esl-erlang elixir

# (5) Use mix to install Phoenix
env PHOENIX_VERSION 1.3.0
run mix archive.install --force https://github.com/phoenixframework/archives/raw/master/phx_new-$PHOENIX_VERSION.ez

# (6) Install hex & rebar locally
run mix local.hex --force \
    && mix local.rebar --force

# Same as before from here on down
add https://raw.githubusercontent.com/isaacs/nave/master/nave.sh ./nave.sh

run bash nave.sh usemain 6.9.1

# ... 
```

Enter fullscreen mode Exit fullscreen mode

一次经历一个变化:

1.  我们将`inotify-tools`和`locales`添加到安装 apt-get install 中。Phoenix 使用`inotify-tools`来检查 Linux 中的文件更改，这允许它在代码更改时重新加载开发服务器。至于`locales`，似乎 Erlang &仙丹喜欢在安装之前正确设置 Unix 语言环境。不知道为什么，我们不做它就抱怨。
2.  我们设置区域信息，如[Marcelo CG/phoenix](https://hub.docker.com/r/marcelocg/phoenix/~/dockerfile/)docker 文件所示。
3.  安装 [erlang-solutions](https://www.erlang-solutions.com/) apt 存储库包。这看起来像是我们在安装一个包，我们确实在安装，但是这个包的结果是让我们的系统知道 erlang-solutions apt 存储库。这允许 apt 从 erlang-solutions 库安装最新的包，而不是潜在的不太新的 Ubuntu 库。
4.  刷新 apt 的信息缓存，安装 Erlang & Elixir。如果您不熟悉 Erlang，它是运行 Elixir 的基础环境。
5.  通过 mix 命令安装 Phoenix。这将从 [Github](https://github.com/) 上引用的归档文件安装 Phoenix。这是 Phoenix 的推荐安装方式。
6.  我们在当地安装六角形和钢筋。Hex 是 Erlang & Elixir 的一个包管理器工具，rebar 是一个构建工具。我不知道这些在我们的映像中是否是必要的，我还没有尝试移除它们。

现在我们可以重建我们的 Docker 映像，它已经准备好运行我们的 Phoenix 开发服务器了。由于更改的性质，重建可能需要一些时间。

## 为我们的 Phoenix 应用程序添加一个 nginx 配置部分

对 nginx 配置的更改非常简单。我们已经有了 Django 应用程序的一个部分。我们只是想要另一个凤凰应用程序。目前，所有请求都发送到 Django 应用程序。作为一个实验步骤，我们将把对`/elixir`的请求重定向到我们的 Phoenix 应用程序。这永远也不会投入生产，但我们仍在准备中。

所以我们的变化看起来是这样的:

```
 # server_name www.tangotimetable.com;
    server_name localhost;

+   location /elixir {
+
+       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
+
+       proxy_set_header Host $http_host;
+
+       # we don't want nginx trying to do something clever with
+       # redirects, we set the Host: header above already.
+       proxy_redirect off;
+
+       proxy_pass http://localhost:4000;
+   }
+
    location / {

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
```

Enter fullscreen mode Exit fullscreen mode

它与下面的 Django 部分相同，除了我们重定向到端口 4000 而不是端口 7000。我不确定，但我相信这一部分出现在文件中 Django 部分的上方是很重要的。这意味着 Django 部分充当所有未发送到 Phoenix 应用程序的请求的总括。

## 让我们的 Phoenix 应用程序在容器中运行

现在我们已经准备好让 Phoenix 开发服务器在我们的容器中运行了。我们将遵循与 Django 应用程序相似的模式。我们将把这个部分添加到我们的[超级管理员](http://supervisord.org/)配置:

```
 stderr_logfile=/dev/stderr
 stderr_logfile_maxbytes=0
+
+[program:phoenix]
+command=/src/dev/run-phoenix
+stdout_logfile=/dev/stdout
+stdout_logfile_maxbytes=0
+stderr_logfile=/dev/stderr
+stderr_logfile_maxbytes=0 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将添加一个类似于
的`run-phoenix`脚本

```
#!/bin/bash

cd /src/timetable

export DOCKER_HOST=`netstat -nr | grep '^0\.0\.0\.0' | awk '{print $2}'`

mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

关于`DOCKER_HOST`环境变量的讨论，参见[上一篇文章](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)。它允许我们的 Phoenix 应用程序找到主机上的 Postgres 数据库，而不是在容器内。

我把它作为一个练习，以确保这个`run-phoenix`文件在 Docker 文件中得到考虑，并使它在 Docker 映像中处于正确的位置。当一个文件没有包含在 Docker 映像中时，这可能会非常令人困惑。

最后要做的更改是在运行时将 Phoenix 应用程序源文件夹放入 Docker 容器。这是因为我们没有将源代码添加到图像中。如果我们这样做了，我们将不得不在每一个变化上重建图像，这将是痛苦的。

所以我们要改变我们的`run-docker-dev`脚本，从[的第一个帖子](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)，像这样:

```
 -v `pwd`/logs/nginx:/var/log/nginx:rw \
    -v `pwd`/logs/cron:/var/log/cron:rw \
    -v `pwd`/dancetimetable:/src/dancetimetable:rw \
+   -v `pwd`/timetable:/src/timetable:rw \
    -e DANCETIMETABLE_SETTINGS=development \
    --rm \
    --interactive \ 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行 Docker 容器并访问`http://localhost:8000`时，我们会看到 Django 应用的首页，但如果我们访问`http://localhost:8000/elixir`，我们会看到凤凰城新项目的登录页面。

*更新 2017-11-12* :我忘了给凤凰路由器做一个必要的改动。我们必须用下面的差异来改变`timetable_web/router.ex`文件:

```
 scope "/", TimetableWeb do
     pipe_through :browser # Use the default browser stack

-    get "/", PageController, :index
+    get "/elixir", PageController, :index
   end

   # Other scopes may use custom stacks. 
```

Enter fullscreen mode Exit fullscreen mode

所以菲尼克斯希望被问及`/elixir`而不是`/`。我们可以改为 nginx，在`/elixir`到达凤凰城之前将其从路线中移除，但这不是我为凤凰城应该处理的路线设定的最终目标。

## 结论

现在我们有了 Django 应用程序&一个新的 Phoenix 应用程序在 Docker 容器中并排运行。我们可以让 Django 应用程序负责一些 URL 路径，让 Phoenix 应用程序负责其他路径。在投入生产之前，我们还有一段路要走，但这是重要的一步！

如果您有任何问题或建议，请在下面评论。