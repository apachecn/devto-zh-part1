# 提供带 Sparrowdo 的码头集装箱

> 原文：<https://dev.to/melezhik/provision-of-docker-containers-with-sparrowdo-8p5>

# 简介

Sparrowdo 是一个基于 [Perl6](https://perl6.org/) 编写的配置管理工具，在这篇文章中，我将向您展示如何通过 Sparrowdo 场景配置(供应)Docker 实例。

# 安装 Sparrowdo

## 安装 Perl6

安装 Perl6 的方法有很多，这里有几个链接可供您参考:

*   [http://rakudo.org/how-to-get-rakudo/](http://rakudo.org/how-to-get-rakudo/)
*   [https://github.com/nxadm/rakudo-pkg/releases](https://github.com/nxadm/rakudo-pkg/releases)

一旦你完成了 Perl6 的安装，剩下的就简单了，你需要安装 Sparrowdo 作为 Perl6 模块:

```
$ zef install Sparrowdo 
```

Enter fullscreen mode Exit fullscreen mode

# 运行 Docker 容器

Sparrowdo 提供实时 Docker 容器，因此您需要运行一个。我将使用 [bitnami/minideb-extras](https://hub.docker.com/r/bitnami/minideb-extras/) ，不过你可以自由选择你自己的:

```
$ docker run -t -d bitnami/minideb-extras bash 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查我们的 Docker 容器，输出将是这样的:

```
$ docker ps
CONTAINER ID        IMAGE                    COMMAND                 CREATED             STATUS              PORTS               NAMES
99bc3937ea37        bitnami/minideb-extras   "/entrypoint.sh bash"   2 seconds ago       Up 1 second                             determined_montalcini 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，我们成功地运行了 Docker 实例。

# 创建 Sparrowdo 场景

Sparrowdo 为服务器配置公开了一个简洁的 DSL，所以我们将介绍一些 Sparrowdo DSL 来展示如何使用它。

本文的其余部分将涉及如何通过各种 Sparrowdo 场景来配置正在运行的 Docker 实例。

我们将讨论一些典型的用例。

## Sparrowfile

默认情况下，Sparrowdo 在当前工作目录中名为`sparrowfile`的文件中查找场景源代码。所以我们将场景代码放在这里。

考虑创建用户和目录的第一个简单用例。

## 创建用户、文件和目录

可能是你在设置服务器时最常做的事情，所以:

```
user "melezhik";

directory "/var/data/dir-one", %(
    owner => "melezhik"
); 
```

Enter fullscreen mode Exit fullscreen mode

然后给出这个场景由 Sparrowdo 运行:

```
$ sparrowdo --no_sudo --docker=determined_montalcini --format=production --bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行将产生*大量的输出*，不要混淆——因为这是全新的服务器，Sparrowdo first install 意味着(*) Sparrow 客户端在此服务器上启动(注意`--bootstrap`选项)。它导致许多补充操作被执行。

第二次运行会快得多，并且会产生更简洁的输出(**):

```
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] create user melezhik OK
push [task] create directory /var/data/dir-one OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//opt/sparrow/task-box.json - OK
public@user is uptodate (0.2.1)
public@directory is uptodate (0.1.5)
running task box from /opt/sparrow/sparrow-cache/task-box.json ... 
2017-10-10 10:22:30 : [task] create user melezhik [path] modules/change/
2017-10-10 10:22:30 : [task] create directory /var/data/dir-one [path] modules/create/ 
```

Enter fullscreen mode Exit fullscreen mode

(*) *[Sparrow 客户端](https://github.com/melezhik/sparrowdo)被用作 Sparrowdo 代理来执行场景，该场景被编译成底层 Sparrow 任务*

(*) *顺便说一句，Sparrowdo 提供了设置报告的选项，请遵循[文档](https://github.com/melezhik/sparrowdo#--format)。*

# 安装系统包

当您进行服务器设置和配置时也经常需要它，所以:

```
package-install (
  'nano',
  'mc',
  'ncdu',
  'nginx'
); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] install packages: nano mc ncdu nginx OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//opt/sparrow/task-box.json - OK
installing public@package-generic version 0.003007 ...
Download https://sparrowhub.org/plugins/package-generic-v0.003007.tar.gz --- 200
running task box from /opt/sparrow/sparrow-cache/task-box.json ... 
2017-10-10 10:35:54 : [task] install packages: nano mc ncdu nginx [path] modules/apt-get/ [params] action:install package:nano
2017-10-10 10:36:01 : [task] install packages: nano mc ncdu nginx [path] modules/apt-get/ [params] action:install package:mc
2017-10-10 10:36:53 : [task] install packages: nano mc ncdu nginx [path] modules/apt-get/ [params] action:install package:ncdu
2017-10-10 10:36:59 : [task] install packages: nano mc ncdu nginx [path] modules/apt-get/ [params] action:install package:nginx 
```

Enter fullscreen mode Exit fullscreen mode

## 运行服务

服务是基础设施和配置的重要组成部分。Sparrowdo 提供了一组期望的函数来处理这个任务。让我们安装 Nginx web 服务器，并将其添加到运行级别，然后启动它。场景将是:

```
package-install 'nginx';
service-enable 'nginx';
service-start 'nginx'; 
```

Enter fullscreen mode Exit fullscreen mode

Sparrowdo 报告是这样的:

```
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] install packages: nginx OK
push [task] enable service nginx OK
push [task] start service nginx OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//opt/sparrow/task-box.json - OK
public@package-generic is uptodate (0.3.7)
installing public@service version 0.001015 ...
Download https://sparrowhub.org/plugins/service-v0.001015.tar.gz --- 200
running task box from /opt/sparrow/sparrow-cache/task-box.json ... 
2017-10-10 10:42:33 : [task] install packages: nginx [path] modules/apt-get/ [params] action:install package:nginx
2017-10-10 10:42:33 : [task] enable service nginx [path] modules/enable/ [params] os:debian service:nginx
2017-10-10 10:42:33 : [task] start service nginx [path] modules/start/ [params] os:debian service:nginx 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，Sparrowdo 提供了一些[漂亮的函数](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md#asserts)来检查服务的健康状况，所以让我们使用一些函数来检查 Nginx 服务器的可用性:

```
proc-exists 'nginx';
http-ok; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们将`--format`更改为默认值，以查看更多详细信息:

```
$ sparrowdo --no_sudo --docker=determined_montalcini --format=default 
```

Enter fullscreen mode Exit fullscreen mode

```
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] check nginx process OK
push [task] run bash: curl -fsSLk -D - --retry 3 127.0.0.1 -o /dev/null ... OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//opt/sparrow/task-box.json - OK
installing public@proc-validate version 0.001003 ...
Download https://sparrowhub.org/plugins/proc-validate-v0.001003.tar.gz --- 200
installing public@bash version 0.001006 ...
Download https://sparrowhub.org/plugins/bash-v0.001006.tar.gz --- 200
running task box from /opt/sparrow/sparrow-cache/task-box.json ... 
2017-10-10 11:26:28 : [task] check nginx process [path] /
check by pid file
pid found at /var/run/nginx.pid:11114
11114 nginx: master process /usr/       43:54
ok  scenario succeeded
ok  text match /.*/
ok  '11114 nginx: master process /usr/       43:54' match /.*/
STATUS  SUCCEED
@ runs bash command
2017-10-10 11:26:28 : [task] run bash: curl -fsSLk -D - --retry 3 127.0.0.1 -o /dev/null ... [path] modules/bash-command/ [params] envvars:
++ test -z
++ bash -c ' curl -fsSLk -D - --retry 3 127.0.0.1 -o /dev/null'
HTTP/1.1 200 OK
Server: nginx/1.6.2
Date: Tue, 10 Oct 2017 11:26:28 GMT
Content-Type: text/html
Content-Length: 867
Last-Modified: Tue, 10 Oct 2017 10:37:28 GMT
Connection: keep-alive
ETag: "59dca2e8-363"
Accept-Ranges: bytes

ok  scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果这些高级抽象还不够，您总是可以执行任意的 Bash 代码，参见下一节。

# 执行 Bash 代码

[Bash](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md#bash-commands) 函数使得插入 Bash 代码片段并执行它们成为可能，有很多方法调用它，你可以添加各种参数，简单的形式就是一个`bash $command`，像这样:

```
bash('echo hello world'); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

Sparrowdo 是一个现代化的包含所有电池的工具包，用于远程配置服务器。正如我们所见，它与 Docker 配合得很好。

有很多内容需要讨论，比如 [ssh/scp](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md#ssh) 命令调用、[获取](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md#git) Git 存储库、通过[模板填充配置文件](https://github.com/melezhik/sparrowdo/blob/master/core-dsl.md#templates)等等，我鼓励你在 Sparrowdo GitHub [文档页面](https://github.com/melezhik/sparrowdo)或 Sparrowdo 专用博客网站-[http://sparrowdo.wordepress.com](http://sparrowdo.wordepress.com)深入研究这些细节

像往常一样-想法，评论和问题是受欢迎的。

问候。

Sparrowdo 的作者。