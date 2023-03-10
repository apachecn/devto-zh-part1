# 如何:GitLab 和 Docker 注册表

> 原文：<https://dev.to/zaptic/how-to-gitlab-and-docker-registry-2moh>

# 如何:GitLab 和 Docker 注册表

前几天下午，我(和另一名工程师)花了太多时间试图在一个自托管的 GitLab 站点上建立一个自托管的 Docker 注册表。

我们发现(文档并没有很好地解释)GitLab 负责运行 Docker 注册表，并确保无论您配置哪个端口都可以访问它。也就是说，你真正需要做的是安装 Docker，并在 GitLab 配置中更改一些东西，然后一切就像变魔术一样发生了。

## 调教

*   我们假设您在某个地方有一个自托管的 GitLab EE 站点，可以通过 gitlab.example.com 访问。
*   我们假设这是在 Ubuntu (16.04)机器上。
*   我们还假设您想要建立一个自托管的 Docker 注册中心，并且您知道这意味着什么。

## 解

### 1。在 GitLab 服务器上安装 Docker

按照此处的说明[安装 Docker。](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)

我将把运行的命令复制出来，以使您免受痛苦，但是如果您不知道这些命令在做什么，请务必检查该链接。

```
$ sudo apt-get update

$ sudo apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  software-properties-common

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

$ sudo apt-key fingerprint 0EBFCD88

$ sudo add-apt-repository \
 "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) \
 stable"

$ sudo apt-get update

$ sudo apt-get install docker-ce 
```

Enter fullscreen mode Exit fullscreen mode

验证 docker 安装正确:

```
$ sudo docker run hello-world 
```

Enter fullscreen mode Exit fullscreen mode

### 2。对 GitLab 配置进行更改

[容器文档](https://docs.gitlab.com/ce/administration/container_registry.html#enable-the-container-registry)说“你所要做的就是配置容器注册中心将监听的域名。请阅读# container-registry-domain-configuration，然后从两个选项中选择一个适合您的情况。

点击他们提供的链接，你会看到[这里](https://docs.gitlab.com/ce/administration/container_registry.html#container-registry-domain-configuration)写着“有两种方法可以配置注册管理机构的外部域名。要么使用现有的 GitLab 域，在这种情况下，注册管理机构必须侦听端口并重用 GitLab 的 TLS 证书，要么使用一个完全独立的域，并为该域提供一个新的 TLS 证书。”

我们正在尝试使用现有的 GitLab 域名，因为——这有多酷？- Docker 注册表登录 GitLab 凭证！

所以，点击带我们去[这里](https://docs.gitlab.com/ce/administration/container_registry.html#configure-container-registry-under-an-existing-gitlab-domain)的链接。

...它说“如果注册表被配置为使用现有的 GitLab 域，您可以在一个端口上公开注册表，以便可以重用现有的 GitLab TLS 证书。”

总结一下。文档说“为容器注册中心配置域名”...如果注册管理机构配置了现有的[域名]”。但是他们完全没有告诉你如何配置域名！这是让我们非常困惑的部分。

#### 缺失的一环

我们假设您不关心要在哪个端口上运行 Docker registry，因此使用 4567。如果您确实关心，请更改该值。

编辑`/etc/gitlab/gitlab.rb`。

找到包含`registry_external_url`的行，将其改为:

```
registry_external_url 'https://gitlab.example.com:4567' 
```

Enter fullscreen mode Exit fullscreen mode

在 nginx 中启用注册表:

```
registry_nginx['enable'] = true
registry_nginx['listen_port'] = 4567 
```

Enter fullscreen mode Exit fullscreen mode

同时复制 TLS 证书行:

```
registry_nginx['ssl_certificate'] = "/path/to/certificate.pem"
registry_nginx['ssl_certificate_key'] = "/path/to/certificate.key" 
```

Enter fullscreen mode Exit fullscreen mode

保存并重新配置。

```
gitlab-ctl reconfigure 
```

Enter fullscreen mode Exit fullscreen mode

GitLab 将自动打开端口，并允许您使用`docker login`和您的 GitLab 凭证/ 2fa 密钥(如果您使用 2fa)登录到注册表。

应该就是这样！祝您快乐:)