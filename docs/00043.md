# 通过 Docker 介绍 Rails 5.1 开发环境

> 原文：<https://dev.to/michiels/introducing-a-rails-51-development-environment-for-docker-9kl>

我正在做一些新的东西:一个易于在 Docker 中设置的 Rails 5.1 开发环境，通过 docker-compose 配置。我在 GitHub 上为此维护了一个模板:[michiels/rails-docker-compose](https://github.com/michiels/rails-docker-compose)。跟随[自述文件，获取在你的应用上设置它的说明](https://github.com/michiels/rails-docker-compose/blob/master/README.md)。

## 为什么？

我为什么要这么做？我希望我们公司团队的所有成员在开发我们开发的众多应用程序中的一个时，不必再考虑本地依赖性。设置一个应用程序应该是几秒钟的事情。

每个应用都有一个开发环境也有助于清理任何数据库和 Sidekiq 队列以及 gem 依赖关系。所以你总是有一个清新干净的环境，你不需要在你的工作站上为每个不同的版本重新编译 Ruby。

## 它包含什么:

我想维护存储库，以便它始终支持 Rails 开发环境，用于:

*   最新的 Ruby on Rails
*   最新的 Ruby 版本
*   一种数据库系统
*   里兹(适用于侧基)
*   RubyGems 用于安装 Gems(标准导轨)
*   用于安装 JS 依赖项的 Yarn(标准 Rails)
*   使用无头 Chrome 进行系统测试

## 如何为你的 app 工作？

你可以在自述文件中阅读[更详细的说明，但要点如下:](https://github.com/michiels/rails-docker-compose/blob/master/README.md)

首先，复制 Dockerfile，docker-compose.yml 文件，并稍微修改 database.yml 和 docker-compose.yml。

然后，您通过
设置应用程序的数据库

```
 $ docker-compose run --rm web rails db:setup 
```

Enter fullscreen mode Exit fullscreen mode

最后，启动一个本地 Rails 开发环境非常容易:

```
 $ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

浏览到 [http://localhost:3000/](http://localhost:3000/) ,你的应用已经启动并运行了 Rails、PostgreSQL、Redis 和 Sidekiq。

额外提示:为生成控制器和模型、运行迁移和测试保持本地开发会话开放。例如:

```
$ bin/devconsole
# rails g controller homepage
# rails db:migrate
# rails test
# rails test:system 
```

Enter fullscreen mode Exit fullscreen mode

## 试试吧！

我很乐意看到这个设置在你的应用程序中工作。让我知道它是成功的还是失败的。