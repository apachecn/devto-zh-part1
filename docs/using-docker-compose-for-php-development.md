# 使用 Docker Compose 进行 PHP 开发

> 原文：<https://dev.to/kellyjandrews/using-docker-compose-for-php-development>

> 这篇文章最初发表在 [Codeship 博客](https://blog.codeship.com/using-docker-compose-for-php-development/)上，经作者允许后发表在这里。

Docker 对于开发者来说是一个神奇的工具。它允许我们在任何主机上构建和复制映像，消除了开发环境的不一致性，并大大缩短了入职时间。

为了提供一个如何转移到容器化开发的例子，我用 PHP、Laravel 和 PostgreSQL 构建了一个简单的`todo` API，使用 [Docker Compose](https://docs.docker.com/compose/) 进行开发、测试，并最终在我的 CI/CD 管道中使用。

在这个由两部分组成的系列中，我将介绍开发和管道创建步骤。在这篇文章中，我将介绍第一部分:用 Docker Compose 开发和测试。

## 对本教程的要求

本教程要求您在开始之前拥有一些物品。

*   安装 [Docker 社区版](https://store.docker.com/search?type=edition&offering=community)
*   安装〔t0〕坞站复合〔t1〕
*   下载[待办事宜应用示例-非 Docker 分支](https://github.com/codeship-library/php-laravel-todoapp/archive/non-docker.zip)

这里的 todo 应用程序本质上是一个替身，你可以用你自己的应用程序替换它。这里的一些设置是特定于这个应用程序的，可能没有涵盖您的应用程序的需求，但它应该是一个很好的起点，让您了解 Dockerize 您自己的应用程序所需的概念。

一旦你设置好了一切，你就可以进入下一部分了。

## 创建 Dockerfile

在任何一个 Dockerized 应用的基础上，你都会发现一个 [`Dockerfile`](https://docs.docker.com/engine/reference/builder/) 。`Dockerfile`包含所有用于构建应用程序映像的指令。您可以通过安装 PHP 及其所有依赖项来设置它，然而，Docker 生态系统有一个图像存储库，其中已经创建了一个 [PHP](https://store.docker.cimg/php?tab=description) 图像，并且可以使用。

在应用程序的根目录下，创建一个新的`Dockerfile`。

```
/> touch Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的编辑器中打开新创建的`Dockerfile`。第一条指令， [`FROM`](https://docs.docker.com/engine/reference/builder/#from) ，将告诉 Docker 使用预构建的 PHP 映像。有几个选择，但是这个项目使用了`php:5.6.30-fpm-alpine`图像。关于我为什么在这里使用`alpine`而不是其他选项的更多细节，你可以阅读[这篇文章](https://blog.codeship.com/alpine-based-docker-images-make-difference-real-world-apps/)。

```
FROM php:5.6.30-fpm-alpine 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行`docker build .`，你会看到类似下面的内容:

```
Sending build context to Docker daemon 1.141 MB
Step 1/1 : FROM php:5.6.30-fpm-alpine
5.6.30-fpm-alpine: Pulling from library/php
709515475419: Pull complete 2cda85d7c7d4: Pull complete dd7a8556500b: Pull complete 96365c659331: Pull complete 2c7770354fce: Pull complete be277856d200: Pull complete 1ffa80d6c3a1: Pull complete abd20be2d28d: Pull complete d8275ff24d93: Pull complete c5f0cf74515d: Pull complete Digest: sha256:8c53cd1d1739023e4fd4b5ee5732ba26fcf9db7c8731275f8eb6bfe780a5b942
Status: Downloaded newer image for php:5.6.30-fpm-alpine
 ---> 4461ceb8e332
Successfully built 4461ceb8e332 
```

Enter fullscreen mode Exit fullscreen mode

does 文件中只有一条指令，这不会做太多，但它确实向您展示了构建过程，而不会发生太多事情。此时，您已经创建了一个图像，运行`docker images`将显示您可用的图像:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
php                 5.6.30-fpm-alpine   4461ceb8e332        8 weeks ago         54.5 MB 
```

Enter fullscreen mode Exit fullscreen mode

`Dockerfile`需要更多的指令来构建应用程序。目前它只是创建一个安装了 PHP 的图像，但是我们仍然需要我们的应用程序代码在容器内部运行。让我们添加更多的指令来完成这个任务，并再次构建这个映像。

这个特定的 Docker 文件使用 [`RUN`](https://docs.docker.com/engine/reference/builder/#run) 、 [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) 和 [`WORKDIR`](https://docs.docker.com/engine/reference/builder/#workdir) 。你可以在 Docker 的参考页面上阅读更多相关内容，以获得更深入的了解。

现在让我们将指令添加到`Dockerfile`中:

```
FROM php:5.6.30-fpm-alpine

RUN apk update && apk add build-base

RUN apk add postgresql postgresql-dev \
  && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
  && docker-php-ext-install pdo pdo_pgsql pgsql

RUN apk add zlib-dev git zip \
  && docker-php-ext-install zip

RUN curl -sS https://getcomposer.org/installer | php \
        && mv composer.phar /usr/local/bin/ \
        && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

COPY . /app
WORKDIR /app

RUN composer install --prefer-source --no-interaction

ENV PATH="~/.composer/vendor/bin:./vendor/bin:${PATH}" 
```

Enter fullscreen mode Exit fullscreen mode

下面是正在发生的事情:

*   更新`apk`包，然后安装`base-build`包
*   添加额外的包，然后更新`PHP`配置并安装扩展
*   下载和设置`composer`
*   将文件从根目录复制到`/app`
*   将工作目录设置为`/app`
*   安装所需的`composer`包
*   设置`PATH`环境变量

您现在可以再次运行`docker build .`，并查看结果:

```
Sending build context to Docker daemon 1.141 MB
Step 1/9 : FROM php:5.6.30-fpm-alpine
 ---> 4461ceb8e332
Step 2/9 : RUN apk update && apk add build-base
 ---> Running in 65b476b2930f
fetch http://dl-cdn.alpinelinux.org/alpine/v3.4/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.4/community/x86_64/APKINDEX.tar.gz
v3.4.6-111-g0e3ca69 [http://dl-cdn.alpinelinux.org/alpine/v3.4/main]
v3.4.6-83-g67e50bc [http://dl-cdn.alpinelinux.org/alpine/v3.4/community]
OK: 5977 distinct packages available

## APK packages installed ##

Executing busybox-1.24.2-r13.trigger
OK: 167 MiB in 44 packages
 ---> cdc2c3372082
Removing intermediate container 65b476b2930f
Step 3/9 : RUN apk add postgresql-dev   && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql   && docker-php-ext-install pdo pdo_pgsql pgsql
---> Running in c841a3c3e218

## APK packages installed ##

Executing busybox-1.24.2-r13.trigger
OK: 200 MiB in 56 packages
---> 12bf760a8c33
Removing intermediate container 1490da0824e9
Step 5/9 : RUN curl -sS https://getcomposer.org/installer | php         && mv composer.phar /usr/local/bin/         && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer
---> Running in 1ba7e9478e8c
All settings correct for using Composer
Downloading...

Composer (version 1.4.1) successfully installed to: /var/www/html/composer.phar
Use it: php composer.phar

---> 2c750fbd7005
Removing intermediate container 1ba7e9478e8c
Step 6/9 : COPY . /app
---> 45fb65033911
Removing intermediate container ec20dc5b20d7
Step 7/9 : WORKDIR /app
---> 52a1093b4739
Removing intermediate container 473e3313e5c3
Step 8/9 : RUN composer install --prefer-source --no-interaction
---> Running in f7f34475a00a

 ## Composer packages installed ##

Generating optimized class loader
The compiled services file has been removed.
 ---> 8f8788e274ab
Removing intermediate container f7f34475a00a
Step 9/9 : ENV PATH "~/.composer/vendor/bin:./vendor/bin:${PATH}"
 ---> Running in e67555179ae3
 ---> 06f03b121b05
Removing intermediate container e67555179ae3
Successfully built 06f03b121b05 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经使用 Docker 成功地创建了应用程序映像。然而，目前，我们的应用程序不会做太多，因为我们仍然需要一个数据库和 [`nginx`](https://www.nginx.com/) ，我们希望将一切连接在一起。这就是 Docker Compose 将帮助我们的地方。

## 码头工复合服务

既然您已经知道了如何使用`Dockerfile`创建一个映像，那么让我们将应用程序创建为一个服务，并将其连接到一个数据库，然后在`nginx`上运行它。然后，我们可以运行一些设置命令，开始创建新的待办事项列表。

创建文件`docker-compose.yml`。

```
/> touch docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

[Docker 组合文件](https://docs.docker.com/compose/compose-file/compose-file-v2/)将基于配置文件定义和运行容器。我们使用的是 [compose file version 2](https://docs.docker.com/compose/compose-file/compose-file-v2/) 语法，你可以在 Docker 的网站上阅读。

需要理解的一个重要概念是 Docker Compose 跨越了“构建时”和“运行时”到目前为止，我们一直在使用`docker build .`，即“构建时间”来构建图像。这是我们的集装箱实际建造的时间。我们可以把“运行时”想象成一旦我们的容器被构建并被使用后会发生什么。

Compose 触发“构建时”——指示我们的映像和容器进行构建——但它也填充“运行时”使用的数据，如 env 变量和卷。清楚这一点很重要。例如，当我们添加像`volumes`和`command`这样的东西时，它们将覆盖那些可能已经在“构建时”通过 docker 文件设置的东西。

在编辑器中打开您的`docker-compose.yml`文件，并复制粘贴以下行:

```
version: '2'
services:
  nginx:
    image: nginx:1.11.10-alpine
    ports:
      - 3000:80
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
  web:
    build: .
    ports:
      - 9000:9000
    volumes:
      - .:/app
      - /app/vendor
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgres://todoapp@postgres/todos
  postgres:
    image: postgres:9.6.2-alpine
    environment:
      POSTGRES_USER: todoapp
      POSTGRES_DB: todos 
```

Enter fullscreen mode Exit fullscreen mode

这需要一点时间来解释，但是让我们按服务来分解一下。

### NGINX 服务

我们本地环境的典型设置需要我们运行某种 web 服务器。对于这个例子，我使用的是`nginx`。像我们使用的 PHP 映像一样，Docker 商店有一个为 [NGINX](https://store.docker.cimg/nginx?tab=description) 预先构建的映像。我们可以使用图像的名称，而不是使用`build`指令，Docker 将为我们获取并使用该图像。在这种情况下，我们使用`nginx:1.11.10-alpine`。

*   `image: nginx:1.11.10-alpine` -与其他服务类似，我们使用的是`alpine`版本。
*   `ports:` -这将向主机发布容器端口，在本例中为`80`，作为端口`3000`。
*   `volumes:` - `nginx`需要一个配置文件。我们可以在本地构建，这将使用本地文件作为`default.conf`文件。

### web 服务

web 服务中的第一个指令是基于我们的`Dockerfile`来`build`图像。这将重新创建我们之前使用的图像，但现在它将根据我们所在的项目文件夹命名为`phplaraveltodoapp`。之后，我们会给这项服务一些具体的指示，告诉它应该如何操作:

*   `volumes:` -此部分将安装主机和容器之间的路径。
*   这将把根目录挂载到我们在容器中的工作目录。
*   `/app/vendor` -这将在运行时连接`/app/vendor`，因此我们不会覆盖已构建的`vendor`目录。
*   `environment:` -应用程序本身期望环境变量`DATABASE_URL`运行。这是以`./config/database.php`为背景的。
*   `ports:` -这将把容器的端口(在本例中为`9000`)作为端口`9000`发送给主机。

`DATABASE_URL`是连接字符串。`postgres://todoapp@postgres/todos`使用`todoapp`用户连接，在主机`postgres`上，使用数据库`todos`。

### Postgres 服务

Docker 存储也有一个为 PostgreSQL 预构建的映像，我们将像使用其他两个一样使用它。

*   这个特殊的图像接受几个环境变量，所以我们可以根据自己的需要定制。
*   `POSTGRES_USER: todoapp` -这会将用户`todoapp`创建为 PostgreSQL 的默认用户。
*   `POSTGRES_DB: todos` -这将创建默认数据库为`todos`。

## 运行应用程序

现在我们已经定义了我们的服务，我们可以使用`docker-compose up`构建应用程序。这将显示正在构建并最终开始的图像。在初始构建之后，您将看到正在创建的容器的名称。

```
Creating network "phplaraveltodoapp_default" with the default driver
Pulling postgres (postgres:9.6.2-alpine)...
9.6.2-alpine: Pulling from library/postgres
627beaf3eaaf: Pull complete e351d01eba53: Pull complete cbc11f1629f1: Pull complete 2931b310bc1e: Pull complete 2996796a1321: Pull complete ebdf8bbd1a35: Pull complete 47255f8e1bca: Pull complete 4945582dcf7d: Pull complete 92139846ff88: Pull complete Digest: sha256:7f3a59bc91a4c80c9a3ff0430ec012f7ce82f906ab0a2d7176fcbbf24ea9f893
Status: Downloaded newer image for postgres:9.6.2-alpine
Building web

## Builds the web image ##

Pulling nginx (nginx:1.11.10-alpine)...
1.11.10-alpine: Pulling from library/nginx
709515475419: Already exists
5142c3ee45c6: Pull complete 6393d8407bbe: Pull complete 0816ea01673a: Pull complete Digest: sha256:aa0daf2b17c370a1da371a767110a43b390a9db90b90d2d1b07862dc81754d61
Status: Downloaded newer image for nginx:1.11.10-alpine
Creating phplaraveltodoapp_postgres_1
Creating phplaraveltodoapp_web_1
Creating phplaraveltodoapp_nginx_1 
```

Enter fullscreen mode Exit fullscreen mode

此时，应用程序正在运行，您将在控制台中看到日志输出。您也可以使用`docker-compose up -d`将服务作为后台进程运行。在开发过程中，我更喜欢在没有`-d`的情况下运行，并创建第二个终端窗口来运行其他命令。如果你想在后台运行它并查看日志，你可以运行`docker-compose logs`。

在新的命令提示符下，您可以运行`docker-compose ps`来查看正在运行的容器。您应该会看到类似下面的内容:

```
 Name                          Command              State               Ports
----------------------------------------------------------------------------------------------------
phplaraveltodoapp_nginx_1      nginx -g daemon off;            Up      443/tcp, 0.0.0.0:3000->80/tcp
phplaraveltodoapp_postgres_1   docker-entrypoint.sh postgres   Up      5432/tcp
phplaraveltodoapp_web_1        docker-php-entrypoint php-fpm   Up      0.0.0.0:9000->9000/tcp 
```

Enter fullscreen mode Exit fullscreen mode

这将告诉您服务的名称、用于启动它的命令、它的当前状态以及端口。注意`phplaraveltodoapp_nginx_1`已经将端口列为`443/tcp, 0.0.0.0:3000->80/tcp`。这告诉我们，您可以在主机上使用`localhost:3000/todos`访问应用程序，我们目前没有公开`443`。

### 迁移数据库模式

不可忽视的一个小但重要的步骤是数据库的模式迁移。Compose 附带了一个 [`exec`](https://docs.docker.com/compose/reference/exec/) 命令，该命令将对正在运行的容器执行一次性命令。迁移模式的典型函数是`php artisan migrate`；我们可以使用`docker-compose exec`在网络服务上运行它。

```
/> docker-compose exec web php artisan migrate

**************************************
*     Application In Production!     *
**************************************

 Do you really wish to run this command? (yes/no) [no]:
 > yes  ## you will need to type 'yes'

Migration table created successfully.
Migrating: 2017_03_24_162139_create_todos_table
Migrated: 2017_03_24_162139_create_todos_table 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以试用 API:

```
/> curl localhost:3000/todos

[] 
```

Enter fullscreen mode Exit fullscreen mode

只要不删除`postgres:9.6.2-alpine`图像，模式和容器中的所有数据都将保持不变。然而，最终，检查一下你的应用程序如何通过干净的设置来构建会是一件好事。你可以运行`docker-compose down`，它会清除已经构建好的东西，让你看到一个新的开始。

请随意查看源代码，尝试一下，看看事情进展如何。

## 测试应用程序

应用程序本身包含一些使用 [`phpunit`](https://phpunit.de/) 构建的集成测试。用 Docker 进行测试有多种方法，包括创建特定于测试环境的`Dockerfile.test`和`docker-compose.test.yml`文件。这有点超出了本文的范围，但是我想向您展示如何使用当前的设置运行测试。

当前容器正在使用项目名`phplaraveltodoapp`运行。这是默认的目录名。如果我们试图运行命令，它将使用相同的项目，容器将重新启动。这是我们不想要的。相反，我们将使用不同的项目名称来运行应用程序，将测试隔离到它们自己的环境中。因为容器是短暂的，所以在一组单独的容器中运行你的测试可以确保你的应用程序在一个干净的环境中完全正常地运行。

在您的终端中，运行以下命令:

```
/> docker-compose -p tests run --rm web phpunit

## Images build ##

PHPUnit 5.7.17 by Sebastian Bergmann and contributors.

......                                                              6 / 6 (100%)

Time: 1.27 seconds, Memory: 16.50MB

OK (6 tests, 16 assertions) 
```

Enter fullscreen mode Exit fullscreen mode

`docker-compose`命令接受几个[选项](https://docs.docker.com/compose/reference/overview/)，后跟一个命令。在这种情况下，您使用`-p tests`来运行`tests`项目名称下的服务。正在使用的命令是 [`run`](https://docs.docker.com/compose/reference/run/) ，将对一个服务执行一次命令。在命令运行后,`--rm`选项将删除容器。最后，我们正在运行`web`服务`phpunit`。

## 结论

此时，您应该已经有了使用 Docker Compose 进行本地应用程序开发的良好开端。在本系列关于使用 Docker Compose 进行 PHP 开发的下一部分中，我将介绍使用 Codeship 集成和部署该应用程序。

你的团队在开发工作流程中使用 Docker 了吗？如果是这样的话，我很想听听你正在做什么，以及你从中看到了什么好处。