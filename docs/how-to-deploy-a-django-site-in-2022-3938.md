# 如何在 2022 年部署 Django 站点

> 原文：<https://dev.to/allmanaj/how-to-deploy-a-django-site-in-2022-3938>

外面是个令人困惑的地方。你已经用 Django 建立了一个很棒的网站，下一步是让它出现在人们面前…但是从哪里开始呢？

在谷歌上快速搜索“部署 Django 网站”会看到一大堆回复，所以今天我会试着把主要的和我个人最喜欢的几个分类。

## Heroku

首先，在部署领域有一个最大的名字: [Heroku](https://www.heroku.com/) 。
Heroku 在其服务器上提供托管解决方案，主要通过终端进行管理。它们是久经考验的行业级主机，非常适合专业人士。

Heroku 提供免费计划以及非常灵活的定价模式，允许用户只为使用的内容付费。

虽然对一些人来说这是一个优雅的解决方案，但根据我的经验，对于没有接触过命令行和在线教程的人来说，这是令人畏惧的。

## 数字海洋市场

接下来是数字海洋。DigitalOcean 在他们的市场中提供了一个解决方案，在他们的系统中为您创建了一个新的[“Django Droplet”](https://marketplace.digitalocean.com/apps/django)，允许您将您的站点上传到他们的容器中，并对其进行配置和部署。
对于那些熟悉命令行和管理 linux 系统的人来说，这是一个很好的解决方案，但是，说实话，学习通过终端管理 linux 服务器的所有知识并不容易。
这个解决方案也是一次性的事情。它为你旋转水滴，就这样。需要的任何进一步的系统管理都是手动完成的，这既耗时又令人困惑(我曾经有过这种经历)。

droplet 的成本并不比所有相同选项的常规 DigitalOcean droplet 高，然而，值得注意的是，这些“Django Droplets”只能在旧金山数据中心进行旋转，这对世界其他地方的一些用户来说可能并不理想。

## PyHost

[PyHost](https://pyhost.io) 提供全面的系统部署*和*系统管理。

该系统允许用户在其任何可用的数据中心创建一个数字海洋服务器。也可以手动连接另一个提供商的 VPS，只要它是正确的操作系统和版本(Debian 11)。PyHost 在你的服务器上动态生成和运行脚本，除了像 DigitalOcean 提供的服务器设置之外，它还允许从 Github、Bitbucket 和 Gitlab 部署单独的站点。如果你不想的话，你永远都不需要访问你的服务器的命令行来启动和运行一个简单的网站。您可以创建新的数据库和管理用户，为您的站点获得一键式 SSL，并通过系统的 UI 配置环境变量。此处提供了大量的文档，将指导您完成整个过程。

您自己托管站点，维护所有的权力，同时永远不需要为终端而烦恼(尽管如果您需要的话，这个选项是可用的)。赢了/赢了！

PyHost 拥有一个免费的计划，允许部署一个带有自动生成的子域的站点，以及具有更大灵活性和功能的付费计划。

## 结论

总之，2022 年现代 Django 开发者有很多选择。部署是一个令人困惑且通常令人生畏的过程，提供的大量选择对此毫无帮助。

如果你喜欢这个终端，并且想要一个能做好几件事的东西，那么 Heroku 和 DigitalOcean 是你最好的选择。然而，如果您想尽可能少地担心系统管理，那么 PyHost 就是您想要的。我真的相信 PyHost 减轻了开发人员的负担，让他们能够专注于对他们来说重要的事情，即代码。因此，走出去，构建令人惊叹的应用程序，不要再为托管而烦恼，因为 PyHost 会支持你