# 在 blue mix 上部署 hugo

> 原文：<https://dev.to/greyhoundforty/deploying-hugo-on-bluemix>

在今天的帖子中，我们将展示如何使用 Cloud Foundry 命令行和构建工具将一个 [Hugo](https://gohugo.io/) 站点部署到 [Bluemix](https://www.ibm.com/cloud-computing/bluemix/) 。本指南假设您已经安装了 Hugo。如果不是这样，请参见 [Hugo 文档](https://gohugo.io/overview/installing/)了解 Hugo 的安装和配置。

您将需要选择一个 Hugo 主题并运行一个构建，这样您的静态文件就会被发送到`public`文件夹中。我在我的示例站点上使用了 heather-hugo 主题，所以命令是:

```
$ hugo -t heather-hugo 
```

Enter fullscreen mode Exit fullscreen mode

生成静态文件后，您可以继续安装和配置 Cloud Foundry cli，将您的应用程序推送到 Bluemix。

## 安装并配置 Bluemix Cloud Foundry cli

安装将取决于您的具体操作系统，但您可以使用[本页](https://github.com/cloudfoundry/cli)来安装命令行界面。安装完成后，您需要使用`cf`命令登录:

```
$ cf login
API endpoint: https://api.ng.bluemix.net

Email> user@example.com

Password>
Authenticating...
OK

Targeted org dev_test

Select a space (or press enter to skip):
1\. dev
2\. testingground

Space> 1
Targeted space dev

API endpoint:   https://api.ng.bluemix.net (API version: 2.40.0)
User:           user@example.com
Org:            dev_test
Space:          dev 
```

Enter fullscreen mode Exit fullscreen mode

## 创建您的清单文件

Cloud Foundry 应用最重要的需求之一是`manifest.yml`文件。这个文件定义了应用程序的元数据。关于清单文件的更多信息可以在这里找到:[部署应用程序清单](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html)。

这是我的 hugo app `manifest.yml`文件

```
applications:
- path: public/
  memory: 1024M
  instances: 1
  name: hugo
  host: hugo
  disk_quota: 1024M
  buildpack: https://github.com/cloudfoundry-incubator/staticfile-buildpack.git 
```

Enter fullscreen mode Exit fullscreen mode

以下是该文件的细目分类:

*   路径:指定要推送的文件夹。如果未定义，则默认为当前目录。
*   内存:指定应用程序需要多少内存。
*   实例:指定推送时要启动的应用程序实例的数量。
*   名称:应用程序的名称。
*   host:这定义了子域的名称(yourhost.myBluemix.net)。
*   buildpack:指定应用程序需要哪种 buildpack。更多信息请点击:[云铸造栈](https://docs.cloudfoundry.org/concepts/stacks.html)

## 将应用推送到 Bluemix

既然我们已经做好了一切准备，我们可以使用 Cloud Foundry cli 将我们的应用程序推送到 Bluemix:

```
$ cf push hugo
Using manifest file /Users/ryan/bluemix/cf-apps/hugo/manifest.yml

Updating app hugo in org user@example.com / space dev as user@example.com...
OK

Using route hugo.testingbig.blue
Uploading hugo...
Uploading app files from: /Users/ryan/bluemix/cf-apps/hugo/public
Uploading 78.9K, 23 files
Done uploading
OK

Stopping app hugo in org user@example.com / space tinylab as user@example.com...
OK

Starting app hugo in org user@example.com / space tinylab as user@example.com...
-----> Downloaded app package (40K)
-----> Downloaded app buildpack cache (4.0K)
Cloning into '/tmp/buildpacks/staticfile-buildpack'...
Submodule 'compile-extensions' (https://github.com/cloudfoundry/compile-extensions.git) registered for path 'compile-extensions'
Cloning into 'compile-extensions'...
Submodule path 'compile-extensions': checked out '26a578c06a62c763205833561fec1c5c6d34deb6'
-------> Buildpack version 1.3.1
Downloaded [https://pivotal-buildpacks.s3.amazonaws.com/concourse-binaries/nginx/nginx-1.9.10-linux-x64.tgz]
grep: Staticfile: No such file or directory
-----> Using root folder
-----> Copying project files into public/
-----> Setting up nginx
grep: Staticfile: No such file or directory
-----> Uploading droplet (2.6M)

0 of 1 instances running, 1 starting
1 of 1 instances running

App started
OK

App hugo was started using this command `sh boot.sh`

Showing health and status for app hugo in org user@example.com / space dev as user@example.com...
OK

requested state: started
instances: 1/1
usage: 1G x 1 instances
urls: hugo.mybluemix.net
last uploaded: Thu Feb 25 17:19:03 UTC 2016
stack: cflinuxfs2
buildpack: https://github.com/cloudfoundry-incubator/staticfile-buildpack.git

     state     since                    cpu    memory       disk         details
#0   running   2016-02-25 11:19:36 AM   0.0%   2.6M of 1G   5.6M of 1G 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Bluemix 中创建自定义域

为了简单的测试和概念验证，你当然可以继续使用`mybluemix.net`域，但是如果你想把它作为一个全职站点，你可以配置 Bluemix 使用一个自定义域。使用自定义域的第一步是在 Bluemix 中创建域，并将其与一个组织相关联。语法是`cf create-domain ORG DOMAIN_NAME`。在我的例子中，ORG 是 tinylab，我的域是 testingbig.blue:

```
$ cf create-domain [ORG] testingbig.blue
Creating domain testingbig.blue for org tinylab as user@example.com...
OK 
```

Enter fullscreen mode Exit fullscreen mode

为了使用您的自定义域，您必须使用`map-route`命令将该域映射到您的 Bluemix 应用程序:

```
$ cf map-route hugo testingbig.blue
Creating route testingbig.blue for org tinylab / space Production_US as user@example.com...
OK 
```

Enter fullscreen mode Exit fullscreen mode

## 将您的自定义域名指向 Bluemix

我的域名 testingbig.blue 目前正在使用 SoftLayer DNS 服务，因此我添加了一条记录，使用令人敬畏的 [SoftLayer CLI](http://softlayer-python.readthedocs.org/en/latest/cli.html) 将我的域名指向 Bluemix DNS 系统。*免责声明:[我在 SoftLayer](https://www.linkedin.com/in/ryan-tiffany-786a036a)T5 工作*

```
$ slcli dns record-add hugo.testingbig.blue @ A 75.126.81.68 
```

Enter fullscreen mode Exit fullscreen mode

## 推送至 Bluemix 使用自定义域

为了在新的自定义域中查看该网站，我们将继续并再次推送该应用程序。

```
$ cf push hugo 
```

Enter fullscreen mode Exit fullscreen mode