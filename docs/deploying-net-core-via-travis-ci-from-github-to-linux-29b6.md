# 正在部署。NET Core 通过 Travis CI 从 GitHub 到 Linux

> 原文：<https://dev.to/collinbarrett/deploying-net-core-via-travis-ci-from-github-to-linux-29b6>

[![.NET Core with Travis CI](img/635918d3d435a58e57b1053b03acf297.png)T2】](https://collinmbarrett.com/deploy-dotnet-core-travis-github-linux/)

在过去的一两个月里，我一直在断断续续地为过滤列表构建一个 API。FilterLists 是一个非货币化的附带项目，所以它把我的日常工作放在次要位置。但是，我也把它作为一个学习的平台。我已经成功构建了一个简单的。NET 核心 API 和配置自动构建，并通过 [Travis CI](https://travis-ci.org/collinbarrett/FilterLists) 部署到 [DigitalOcean](https://www.digitalocean.com) Ubuntu VPS。下面是该过程的快速总结。

## 用 GitHub 连接 Travis CI

一旦我连接了我的 [Travis](https://travis-ci.org/) 和 [GitHub](https://github.com/) 账户，我所有的公共存储库都显示为用 Travis 构建的潜在资源。但是，首先，我必须在存储库中创建一个. travis.yml 文件，它为 travis 提供所有的配置设置。

这个配置文件包含一些参数，比如编写应用程序的语言、Travis 应该构建的 Linux 发行版、的版本。NET 来编译、加密部署目标的凭证(Travis 使用 Ruby Gem 提供了一个[加密过程)等等。这个文件的大部分内容都是显而易见的，但是它确实经过了大量的尝试和错误才变得正确。](https://docs.travis-ci.com/user/encryption-keys/)

## 建筑

我将 Travis 配置为在每次提交到 GitHub 存储库的主分支后自动触发新的构建和部署。在。yml 文件，我将 Travis 指向构建脚本。建筑用。NET Core 超级容易，你也看到了。`dotnet restore`处理 NuGet 包，`dotnet publish`构建自包含的可部署应用程序。我也可以在这里执行我的单元测试；但是，目前，我的单元测试项目是空的。

## 正在部署

假设构建成功，Travis 就会查看我的部署脚本。脚本的第 3 行授予对主应用程序. dll 的执行权限，否则，对 API 的请求将失败。第 4 行是通过 SCP 部署到我的 VPS。请注意，我目前正在从。yml 文件。将来，我计划用 SSH 密钥取代用户名/密码认证。

但是有一个文件 Travis 无法部署。appsettings。Production.json 包含生产数据库的连接字符串。由于显而易见的原因，这些信息不应该提交给公共存储库。不过，这个文件很少更改，所以一旦我手动将它放到服务器上的正确位置，它就不需要 Travis 自动更新了。

## 向世界曝光(NGINX)

一旦发布的应用程序位于我的服务器上，最后一步就是将它暴露在公共互联网上。我在同一个机器上为各种其他网站使用 NGINX，所以很容易就能创建一个新的服务器模块，将 api.filterlists.com 的流量导向我的网站。NET 核心应用程序。

## 很管用

现在，您可以通过发出 HTTP Get 请求[获得数据库中所有列表的 JSON 响应。](https://filterlists.com/api/v1/lists)