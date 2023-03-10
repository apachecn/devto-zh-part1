# 姜戈到凤凰城-第 0 部分:背景

> 原文：<https://dev.to/michaeljones/django-to-phoenix-part-0-6ij>

嘿，我写了一个网站！我对 web 开发很陌生，但我尽力了。我是用 [Django](https://www.djangoproject.com/) & [Python](https://www.python.org/) 写的，因为我已经很熟悉 Python 了，Django 是一个友好的框架，拥有[良好的生态系统](https://djangopackages.org/)。这是一次很棒的经历。作为一个初学者，我很高兴有姜戈一路握着我的手。

然而，时间已经过去，我正在进入函数式编程。虽然 Python 中的[函数技术是可能的，但感觉不太适合。我也一直在阅读和听人们谈论](https://docs.python.org/2/howto/functional.html)[凤凰](http://phoenixframework.org/)、[仙丹](http://elixir-lang.org/)、[二郎](https://www.erlang.org/)虚拟机和组合听起来很棒。

所以，我打算至少把我的网站[探戈时间表](https://www.tangotimetable.com/)的一部分从姜戈搬到凤凰城。

## 起点

该网站完全位于 Django，有几个负责每周邮件的 cron jobs。它在 Docker 容器中使用 [Nginx](https://www.nginx.com/resources/wiki/) 后面的 [Gunicorn](http://gunicorn.org/) 运行。我使用 Docker 来简化部署，因为我发现“git-pull & restart”方法太脆弱了。

## 免责声明

我对 Python 很熟悉，在 Django 我很有经验，但不是专家，在 Phoenix & Elixir 我是初学者。我将在以后的帖子中记录我的努力，分享我的发现，同时也希望能从其他可能知道得更多的人那里得到指导。