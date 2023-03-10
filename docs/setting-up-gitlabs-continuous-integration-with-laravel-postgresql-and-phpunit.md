# 设置 GitLab 与 Laravel、PostgreSQL 和 PHPUnit 的持续集成

> 原文：<https://dev.to/ariera/setting-up-gitlabs-continuous-integration-with-laravel-postgresql-and-phpunit>

我最近为一个基于 Laravel 和 PostgreSQL 的项目建立了一个基于 GitLab 的持续集成过程。它花了一段时间来得到它的权利，所以我在这里分享我的最终配置。

## 基本结构

主 GitLab CI 配置保存在项目根目录下的一个文件中，这个文件应该叫做`.gitlab-ci.yml`。从这个文件中可以调用其他 shell 脚本，但是我试图将所有内容都放在一个地方。

然而，我们将管理第二个名为`.env.gitlab-ci`的文件，它将保存特定于 GitLab CI 的所有环境变量配置选项。

最后但同样重要的是，您需要一个 PHPUnit xml 配置文件。

## 一步步看向`.gitlab-ci.yml`

我们将在配置文件中一行一行地进行，但是您可以在文章末尾看到最终结果。

```
image: php:7.0 
```

Enter fullscreen mode Exit fullscreen mode

这里我们选择一个已经配置了 php 的 docker 图像。你可以在这里找到完整的名单:从[https://hub.docker.com/r/\_/php/](https://hub.docker.com/r/%5C_/php/)

```
services:
  - postgres:latest 
```

Enter fullscreen mode Exit fullscreen mode

来自 GitLab 的官方文档 [src](https://docs.gitlab.com/ce/ci/docker/using_docker_images.html#what-is-a-service) :

> `services`关键字定义了另一个 docker 图像，它在您的作业期间运行，并链接到`image`关键字定义的 docker 图像。这允许您在构建时访问服务映像。

需要注意的是**非常重要**如果您将`postgres`作为服务添加到您的应用程序中，PostgresSQL 的服务容器将可以在主机名`postgres`下访问。因此，为了访问您的数据库服务，您必须连接到名为`postgres`的主机，而不是套接字或`localhost`。当我们稍后配置我们的`.env.gitlab-ci`文件时，这将是很重要的。

```
variables:
  POSTGRES_DB: mydb-test
  POSTGRES_USER: runner
  POSTGRES_PASSWORD: "" 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您可以使用您想要的所有值，只要它们与`.env.gitlab-ci`和您的`phpunit.xml`相匹配。

```
before_script: 
```

Enter fullscreen mode Exit fullscreen mode

同样来自官方文档 [src](https://docs.gitlab.com/ce/ci/yaml/README.html#before_script)

> `before_script`用于定义应该在所有作业之前运行的命令，包括部署作业，但是在工件恢复之后。这可以是数组或多行字符串。

让我们逐一研究所有这些命令。

```
- >
  set -xe
  && apt-get update -yqq
  && apt-get install -yqq
  git
  libicu-dev
  libpq-dev
  libzip-dev
  zlib1g-dev 
```

Enter fullscreen mode Exit fullscreen mode

简单地安装一些依赖项

*   稍后安装`composer`时需要`git`
*   `libicu-dev`将是国际化 php 扩展(`intl`)所必需的
*   是 postgres 的头文件
*   `libzip-dev`和`zlib1g-dev`是 zip 操作所必需的，并且需要激活相应的 php 扩展。如果没有它们，我们的`composer install`命令将抛出很多警告，并试图下载每个包两次**减慢 whol 进程 2 分钟或更多时间**

```
- >
  docker-php-ext-install
  pdo_pgsql
  pgsql
  sockets
  intl
  zip 
```

Enter fullscreen mode Exit fullscreen mode

`docker-php-ext-install`是一个由官方 php docker 镜像提供的安装 php 扩展的便捷脚本。我将自己限制在项目运行所需的最低限度的扩展。你可能需要更多。

```
- curl -sS https://getcomposer.org/installer | php
- php composer.phar self-update
- php composer.phar install --no-progress --no-interaction 
```

Enter fullscreen mode Exit fullscreen mode

安装`composer`并使用它来安装我们所有的项目依赖项。

```
- cp .env.gitlab-ci .env 
```

Enter fullscreen mode Exit fullscreen mode

设置正确的环境变量。我们一会儿会调查这份文件。

```
- php artisan key:generate
- php artisan help config:clear
- php artisan route:clear
- php artisan migrate:refresh 
```

Enter fullscreen mode Exit fullscreen mode

设置应用程序密钥，清除几个缓存以防万一，然后运行迁移。

```
test:app:
  script:
  - vendor/bin/phpunit --configuration phpunit-gitlabci.xml 
```

Enter fullscreen mode Exit fullscreen mode

最后使用我们的`phpunit-gitlabci.xml`配置文件运行我们的 PHPUnit 测试套件。

## 小破

这是最复杂的文件，其余的是小菜一碟，但你值得休息:P

## 一探究竟`.env.gitlab-ci`

这个文件本身是很容易解释的，你可以在文章的最后看到最终版本。但是我想强调一下数据库配置。

```
DB_CONNECTION=pgsql
DB_PORT=5432
DB_HOST=postgres
DB_DATABASE=mydb-test
DB_USERNAME=runner
DB_PASSWORD= 
```

Enter fullscreen mode Exit fullscreen mode

请注意`DB_HOST`、`DB_DATABASE`和`DB_USERNAME`的值与`.gitlab-ci.yml`配置文件中`variables`键内的值相同。

## 简述`phpunit-gitlabci.xml`

根据您的本地配置，该文件可能是可选的。如果你的普通`phpunit.xml`文件没有定义任何`DB_DATABASE` env 变量，你就不需要它。但是如果它恰好定义了一个，并且与您为 GitLab 的 CI 选择的值不同，您必须记得正确设置它:

```
<env name="DB_DATABASE" value="mydb-test"/> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

GitLab 的持续集成系统一旦正确配置可能是一个福音，但是如果你偏离了通常的路径，并且你所听到的都是 *docker 镜像配置链接*并且你以前从未使用过它，它可能会变得有点混乱。希望你会发现这个小指南很有用

。gitlab-ci.yml

```
image: php:7.0
services:
  - postgres:latest
variables:
  POSTGRES_DB: mydb-test
  POSTGRES_USER: runner
  POSTGRES_PASSWORD: ""

before_script:
- >
  set -xe
  && apt-get update -yqq
  && apt-get install -yqq
  git
  libicu-dev
  libpq-dev
  libzip-dev
  zlib1g-dev
- >
  docker-php-ext-install
  pdo_pgsql
  pgsql
  sockets
  intl
  zip
- curl -sS https://getcomposer.org/installer | php
- php composer.phar self-update
- php composer.phar install --no-progress --no-interaction
- cp .env.gitlab-ci .env
- php artisan key:generate
- php artisan help config:clear
- php artisan route:clear
- php artisan migrate:refresh

test:app:
  script:
  - vendor/bin/phpunit --configuration phpunit-gitlabci.xml 
```

Enter fullscreen mode Exit fullscreen mode

. env.gitlab-ci

```
APP_ENV=testing
APP_KEY=key
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=http://localhost:8000

DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=mydb-test
DB_USERNAME=runner
DB_PASSWORD=

BROADCAST_DRIVER=log
CACHE_DRIVER=array
SESSION_DRIVER=array
QUEUE_DRIVER=sync
MAIL_DRIVER=log 
```

Enter fullscreen mode Exit fullscreen mode

*本帖最初发表于[ariera . github . io](http://ariera.github.io/2017/03/09/settingup-gitlab-ci-with-laravel-postgres-phpunit.html)T3】*