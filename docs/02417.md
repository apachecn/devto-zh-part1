# 使用 Puma 在 Heroku Free Dyno 上运行 Sidekiq

> 原文：<https://dev.to/bilalbudhani/running-sidekiq-on-heroku-free-dyno-with-puma-3oc>

在我看来， [Heroku](https://www.heroku.com) 是部署项目的近乎完美的地方。它支持许多开箱即用的技术，只需很少的配置或不需要配置。

同样，每当需要后台作业处理时， [Sidekiq](https://sidekiq.org) 是我强烈推荐的。它附带了所有正确的 API 集来处理后台处理，更重要的是，它还有一个很棒的监控仪表板。

所以，上周末我决定做一个我一直想做的兼职项目(我会在另一篇博文中写这个项目)。我推出了一个新的 rails 项目，用 Redis 添加了 Sidekiq，并拼凑了一个基本的功能原型。当我转到部署流程时，我意识到——我必须运行另一个 dyno 来保持 Sidekiq 流程运行。

现在让我在这里诚实地告诉你，我有一点创造副业的想法。一旦我遇到一个问题，我会花一些时间去解决它&之后我会努力找到时间重新开始这个项目。这就是为什么我在项目初期不参与任何付费计划。

回到我们手头的问题，Heroku 已经提供了一个免费的 Redis 插件—

```
heroku addons:create heroku-redis:hobby-dev 
```

Enter fullscreen mode Exit fullscreen mode

另一半是弄清楚如何在同一个 dyno 中生成一个 Sidekiq 进程。经过一番挖掘后，我在 Heroku 上找到了一篇很棒的博客文章 [Sidekiq 和 Redis To Go Nano](http://manuelvanrijn.nl/blog/2012/11/13/sidekiq-on-heroku-with-redistogo-nano/) 描述了如何在 Unicorn 中实现同样的目标。现在的问题是如何在彪马身上复制这种行为。

...后来的一些实验...

我让`config`中的`puma.rb`模仿同样的行为——检查 Heroku Redis 的客户端连接限制。这里是配置:

```
threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }

threads threads_count, threads_count

port        ENV.fetch("PORT") { 3000 }

environment ENV.fetch("RAILS_ENV") { "development" }

workers ENV.fetch("WEB_CONCURRENCY") { 2 }

preload_app!

on_worker_boot do
  @sidekiq_pid ||= spawn('bundle exec sidekiq -c 2 -t 25')
  ActiveRecord::Base.establish_connection if defined?(ActiveRecord)
end

on_restart do
  Sidekiq.redis.shutdown { |conn| conn.close }
end

plugin :tmp_restart 
```

Enter fullscreen mode Exit fullscreen mode

将此复制到您的 Puma 配置中，并使用 Heroku 应用程序进行部署。

```
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

确保将`Procfile`指向右边的 puma 配置

```
bundle exec puma -C config/puma.rb 
```

Enter fullscreen mode Exit fullscreen mode

**帖子最初发布于[https://bilalbudhani . com/running-sidekiq-on-heroku-free-dyno/](https://bilalbudhani.com/running-sidekiq-on-heroku-free-dyno/)**

页（page 的缩写）我可以为项目提供咨询。