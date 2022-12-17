# 为基于 Docker 的开发设置 GitLab

> 原文：<https://dev.to/hanzeljesheen/setup-gitlab-for-docker-based-development>

Gitlab 被称为开源 git 存储库管理器。然而，Gitlab 现在做的远不止这些。它有一个非常强大的 CI/CD 引擎，甚至还打包了一个 docker 注册表。这已经成为我发展过程中必不可少的一部分。在本文中，我们将讨论为基于 docker 的开发设置 Gitlab。

我们将从在云中设置一个 VM 并在那里安装 Gitlab 开始。我们将对其进行配置以支持 HTTPS。我们还将设置 [Gitlab CI](https://about.gitlab.com/gitlab-ci/) ，这是 Gitlab 附带的持续集成解决方案，有多个运行器并行运行构建。我们还将设置 docker 注册表来存储我们的 docker 图像，并用 HTTPs 保护它。最后，我们将使用基于 docker 的项目测试这个设置。

对于 cloud VM，我使用的是 [Digital Ocean](https://www.digitalocean.com/) ，但是这个过程对于其他厂商来说也是类似的。我们将使用[来加密](https://letsencrypt.org/)来生成 SSL 证书。要使用它，您还需要一个域。用于测试 gitlab 设置的项目在这里由[主持。用](https://github.com/botleg/gitlab-nginx) [nginx](https://www.nginx.com/) webserver 构建 docker 映像是一个简单的项目。

## 创建虚拟机

使用您的云服务创建虚拟机，我正在使用数字海洋。您也可以在您的本地系统中托管 Gitlab。我选择`Ubuntu 16.04 x64`作为操作系统，但是任何 linux 发行版都可以。此外，因为我们将整个设置放在一个虚拟机中，所以至少要有 8GB 的内存。

我们将用一个子域注册这个 Gitlab 实例。例子:`gitlab.botleg.com`。创建虚拟机后，在 DNS 提供商中创建一个新的`A`记录。该名称将是子域名，`gitlab`在这里，并提供虚拟机的公共 IP。SSH 进入 VM 创建并创建一个新用户。按照[本](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)教程添加该用户并设置无密码访问。

## 安装 Gitlab

我们将使用 Gitlab 的社区版。这个版本支持我们开发过程中需要的所有特性。版本对比可以在[这里](https://about.gitlab.com/products/#compare-options)找到。安装 Gitlab 最简单的方法是使用 Omnibus 包。它将 Gitlab 所需的一切都包含在一个包中，并配有[厨师](https://www.chef.io/)的任务食谱。

只要按照[这个](https://about.gitlab.com/downloads/#ubuntu1604)站点的步骤安装就可以了。您只需要安装一些依赖项，添加新的存储库并安装`gitlab-ce`包。命令`sudo gitlab-ctl reconfigure`触发了一个 chef receipe，它根据对配置文件所做的更改重新配置 Gitlab。

一旦完成，你将能够在 DNS 服务中进入域名设置并看到 Gitlab 登录页面。您将被要求为名为`root`的默认管理员帐户设置密码。设置管理员密码并登录 Gitlab。现在，我建议您创建一个新的管理员用户，并在接下来的步骤中使用它。这可以从管理区完成。

## 启用 HTTPS

要为 Gitlab 启用 HTTPS，我们需要为该域生成一个证书。为此，我们将使用`Let's Encrypt`。要了解更多，请访问这里的。我们将从安装`Let's Encrypt`和
开始

```
sudo apt-get install letsencrypt 
```

Enter fullscreen mode Exit fullscreen mode

要创建证书，我们需要验证我们确实拥有该域。为此，`letencrypt` tool 提供了一个插件`standalone`,用于创建一个临时 web 服务器并验证域。因此，要实现这一点，我们需要暂时禁用 gitlab。为此，使用以下命令并在提示时输入域名和电子邮件

```
sudo gitlab-ctl stop
sudo letsencrypt certonly --standalone --agree-tos
sudo gitlab-ctl start 
```

Enter fullscreen mode Exit fullscreen mode

生成的证书将放在文件夹`/etc/letsencrypt/live/<domain name>/`中。证书文件为`fullchain.pem`，密钥为`privkey.pem`。我们需要改变 Gitlab 的配置。该文件位于`/etc/gitlab/gitlab.rb`。这个文件充满了注释。我们将备份这个文件并清除它，

```
sudo cp /etc/gitlab/gitlab.rb /etc/gitlab/gitlab.rb.bak
sudo truncate -s 0 /etc/gitlab/gitlab.rb 
```

Enter fullscreen mode Exit fullscreen mode

我们需要添加以下配置项目:

*   `external_url`:可以访问 gitlab 的 URL。它将包含`https://`，后跟域名。
*   `nginx['redirect_http_to_https']`:我们将它设置为`true`，将所有 HTTP 流量重定向到 HTTPS。
*   `nginx['ssl_certificate']`:证书文件的位置。
*   `nginx['ssl_certificate_key']`:证书密钥的位置。

以 root 用户身份打开文件`/etc/gitlab/gitlab.rb`并输入以下几行。

```
external_url 'https://<domain name>'
nginx['redirect_http_to_https'] = true nginx['ssl_certificate'] = "/etc/letsencrypt/live/<domain name>/fullchain.pem"
nginx['ssl_certificate_key'] = "/etc/letsencrypt/live/<domain name>/privkey.pem" 
```

Enter fullscreen mode Exit fullscreen mode

将`<domain name>`替换为 Gitlab 注册的域名。使用命令
重新配置 Gitlab 的新设置

```
sudo gitlab-ctl reconfigure 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们去 Gitlab 网站的时候，它将会在 HTTPS 获得`Let's Encrypt`证书。

## Gitlab CI Runners

在这一步中，我们将设置运行 CI/CD 构建所需的运行程序。运行者将构建、测试并发布由`.gitlab-ci.yml`文件定义的项目。我们将在后面详细讨论这一点。因为我们正在做基于 docker 的项目，所以我们将为跑步者使用 docker 图像，并使用`Docker inside Docker`来运行我们的构建。

第一步是在这个服务器上安装 docker 引擎，

```
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker $USER 
```

Enter fullscreen mode Exit fullscreen mode

第二个命令是在没有`sudo`的情况下访问 docker。为此，您可能需要注销并再次登录。一旦我们安装了 docker 引擎，我们将运行 [Gitlab CI Multi Runner](https://hub.docker.com/r/gitlab/gitlab-runner/) 映像。我们将设置映像在关闭时总是重启。有了这个实现，我们将与 runner 共享服务器中的 docker 引擎。为此，我们将在位置`/var/run/docker.sock`创建一个卷。要做到这一切，使用下面的命令，

```
docker run -d --name runner1 --restart always -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest 
```

Enter fullscreen mode Exit fullscreen mode

这里，`runner1`是这个跑者的名字。您可以运行多个运行程序来并行运行构建。要运行更多的跑步者，通过将名称`runner1`改为其他名称来运行之前的命令。例如:`docker run -d --name runner2 --restart always -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest`等等。

要将这些跑步者注册到 Gitlab 服务，您需要有注册令牌。你可以在管理区的`Runners`部分找到它。一旦找到它，运行下面的命令，

```
docker exec -it runner1 gitlab-runner register 
```

Enter fullscreen mode Exit fullscreen mode

该命令将询问以下内容:

*   `gitlab-ci coordinator URL`:带有`https://`的 gitlab 服务的域名，例如:`https://gitlab.botleg.com`。
*   `gitlab-ci token`:输入我们从 Gitlab 网站得到的注册令牌。
*   给这个跑步者起个名字。例子:`runner1`。
*   给这个跑步者一些标签。Gitlab 提供了在带有某些标签的 runner 上运行测试的选项。
*   `run untagged builds`:布尔值，表示该运行器是否可以接受没有标签的作业。由于我们这里只有一种类型的流道，我们可以为此提供`true`。
*   `executor`:跑步者如何执行作业。这里，我们选择`docker`。更多关于其他执行人的信息，请点击[这里](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/executors/README.md)。
*   `default Docker image`:运行测试的 docker 图像。为此，我们将使用`docker:git`。这张图片支持`docker in docker`，也内置了 git。

通过更改跑步者名称，为您的所有跑步者重复此命令。例子:`docker exec -it runner2 gitlab-runner register`。这样做之后，你就可以在 Gitlab 的管理区的`Runners`部分看到这些跑步者了。

然而，我们还需要做一步。我们将服务器中的 docker 引擎共享给跑步者。这些运行者将创建`docker:git`映像来运行作业。这些作业构建并推送 docker 映像。我们将使用服务器上的 docker 引擎来完成这项工作。因此，我们需要为跑步者创建的`docker:git`图像创建一个`/var/run/docker.sock`的 docker 卷来共享 docker 引擎。为此，我们需要修改每个跑步者的配置。运行下面的命令来打开运行器的配置文件。

```
docker exec -it runner1 nano /etc/gitlab-runner/config.toml 
```

Enter fullscreen mode Exit fullscreen mode

将行`volumes = ["/cache"]`编辑为`volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock"]`。这个文件看起来会像这样:

```
concurrent = 1
check_interval = 0

[[runners]]
  name = "runner1"
  url = "https://gitlab.botleg.com"
  token = "9ab32be14c9d2cb67fbec7aa59304f"
  executor = "docker"
  [runners.docker]
    tls_verify = false image = "docker:git"
    privileged = false disable_cache = false volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock"]
  [runners.cache] 
```

Enter fullscreen mode Exit fullscreen mode

对其他跑步者也重复这个步骤。

## 集装箱登记

我们需要设置的最后一件事是[容器注册表](https://docs.gitlab.com/ce/user/project/container_registry.html)。我们将为注册表创建一个新的子域，如`registry.botleg.com`，并使用 HTTPS 保护它。所以，第一件事就是用 DNS 服务创建一个新的`A`记录。在这种情况下，名称将是`registry`，并给出虚拟机的公共 IP。现在，我们的服务器有了`nginx` webserver。如果请求是针对`gitlab`子域，它将重定向到 gitlab，如果请求是针对`registry`，它将重定向到 registry。

我们还需要为这个`registry`子域创建新的 SSL 证书。和以前一样，我们使用 Let's Encrypt。

```
sudo gitlab-ctl stop
sudo letsencrypt certonly --standalone --agree-tos
sudo gitlab-ctl start 
```

Enter fullscreen mode Exit fullscreen mode

出现提示时，提供注册表的域。这将创建新的 SSL 证书，可以在`/etc/letsencrypt/live`文件夹中找到。更新 gitlab 配置文件`/etc/gitlab/gitlab.rb`以添加以下几行。

```
registry_external_url 'https://<domain name>'
registry_nginx['ssl_certificate'] = "/etc/letsencrypt/live/<domain name>/fullchain.pem"
registry_nginx['ssl_certificate_key'] = "/etc/letsencrypt/live/<domain name>/privkey.pem" 
```

Enter fullscreen mode Exit fullscreen mode

将`<domain name>`替换为在注册表中注册的域名。这将启用 HTTPS 注册。要更新更改，使用`sudo gitlab-ctl reconfigure`命令。在管理区域中，您可以看到容器注册表已启用。要对此进行测试，请尝试登录注册表。

```
docker login <registry domain> 
```

Enter fullscreen mode Exit fullscreen mode

将`<registry domain>`替换为在注册表中注册的域名，并提供 Gitlab 用户名和密码。可以看到`Login Succeeded`消息。

## 测试设置

我们现在已经为基于 docker 的开发设置了 Gitlab。为了测试这个实现，我们将把一个 git 存储库推送到 Gitlab，看看 Gitlab CI 和容器注册的工作情况。我做了一个简单的基于 docker 的项目，可以在这里找到[。这只是包含一个安装了为`index.html`文件服务的`nginx` websever 的`Dockerfile`。](https://github.com/botleg/gitlab-nginx)

我们感兴趣的文件是`.gitlab-ci.yml`。这包含了关于如何构建和部署这个项目的所有信息。要了解更多关于这个文件的信息，请点击[这里](https://docs.gitlab.com/ce/ci/yaml/)。该项目的文件如下所示。

```
image: docker:git

stages:
- build
- publish

build:
  stage: build
  script:
    - docker build -t $REGISTRY_HOST/$IMAGE_NAME .

registry:
  stage: publish
  script:
    - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN $REGISTRY_HOST
    - docker push $REGISTRY_HOST/$IMAGE_NAME 
```

Enter fullscreen mode Exit fullscreen mode

由于该作业是基于 docker 的，因此我们将指定运行该测试的映像。图像`docker:git`里面包含了`docker`和`git`。我们可以将整个过程分成多个阶段，每个阶段包含多个任务。每个作业都是一个阶段，将并行完成，并且只有在前一个阶段中的所有作业都成功时，才会触发每个阶段。

这里，我们有两个阶段，`build`和`publish`。作业`build`处于`build`阶段，作业`registry`处于`publish`阶段。因此，`registry`作业只有在`build`作业成功时才会完成。`build`作业包含一个 bash 命令来构建 docker 映像。我们可以通过在脚本前面加上`$`来使用变量。这个变量的值可以从 Gitlab UI 中给出。图像名称将为`$REGISTRY_HOST/$IMAGE_NAME`。这里，`$REGISTRY_HOST`是注册域名，`$IMAGE_NAME`是存储库名称。

在`registry`作业中，我们将这个图像推送到容器注册中心。为此，我们必须登录到注册表。Gitlab 为此在变量`$CI_BUILD_TOKEN`中有一个名为`gitlab-ci-token`的临时用户和密码。一旦在`$REGISTRY_HOST`登录到注册表，我们就可以推送 docker 映像。

为了测试这一点，通过从 Github 导入，在 Gitlab 中创建新的公共项目。在设置中，选择`Variables`并添加以下两个变量。

*   `REGISTRY_HOST`:容器注册注册的域。例子:`registry.botleg.com`。
*   `IMAGE_NAME`:项目的存储库名称。例子:`botleg/gitlab-nginx`。

一旦存储库被导入，转到`Pipelines`选项卡并点击`Run Pipeline`按钮来触发构建。您可以看到作业正在运行，映像被推送到注册表。一旦构建管道完成，您可以在项目的`Registry`部分看到 docker 图像。

## 证书更新

由 Let's Encrypt 生成的证书将在 90 天后过期。因此，我们必须自动更新这些证书。让我们加密提供一个命令`letsencrypt renew`来做到这一点。此命令将检查证书是否即将过期，并为这些证书进行续订。为了进行更新，我们需要暂时停止 Gitlab 服务。

以下命令将更新证书，

```
gitlab-ctl stop
letsencrypt renew
gitlab-ctl start
gitlab-ctl reconfigure 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为`root`用户添加一个 cron 作业。用命令`sudo crontab -e`打开`root`用户的 crontab 并粘贴下面一行。

```
0 7 1 * * (gitlab-ctl stop && letsencrypt renew && gitlab-ctl start && gitlab-ctl reconfigure) >> /var/log/report.log 2>&1 
```

Enter fullscreen mode Exit fullscreen mode

这将导致该任务在每月 1 日上午 7 点运行，并将输出记录到文件`/var/log/report.log`。现在，我们已经为基于 docker 的开发完全设置了 Gitlab，并且用 git repo 测试了它。