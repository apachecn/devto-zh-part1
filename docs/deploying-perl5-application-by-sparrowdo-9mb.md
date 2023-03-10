# 通过 Sparrowdo 部署 Perl5 应用程序

> 原文：<https://dev.to/melezhik/deploying-perl5-application-by-sparrowdo-9mb>

Sparrowdo -是一个在 Perl6 上编写的配置管理工具。这是自动化服务器部署的有效工具。你写过 Perl5 应用吗？不要为寻找合适的部署工具而烦恼。斯帕罗多来救援了！

在这篇文章的剩余部分，我将一步一步地介绍如何使用 Sparrowdo 部署带有从 Git 获取的源代码的[mojolicous](http://mojolicious.org/)应用程序。我们要做的是:

*   从 GitHub 库获取源代码。
*   使用 [Carton/cpanfile](https://metacpan.org/pod/Carton) 安装 CPAN 依赖项。
*   保存所有文件，并在专门的系统用户下运行流程。
*   设置 systemd 脚本，将 web 应用程序作为守护进程运行。
*   建立一个带有密码的文件，用于应用程序的[摘要访问认证](https://en.wikipedia.org/wiki/Digest_access_authentication)。

你准备好了吗？我们走吧。

# Sparrowdo 安装

Sparrowdo 是 Perl6 模块，所以由 zef manager 安装:

```
$ zef install Sparrowdo 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了 Sparrowdo，我们应该开始编写 Sparrowdo 场景，让我们创建一个名为`sparrowfile`的文件，位于当前工作目录:

```
$ nano sparrowfile 
```

Enter fullscreen mode Exit fullscreen mode

我们在目标主机上远程运行 sparrowdo 场景，如下所示:

```
$ sparrowdo --host=$remote-host 
```

Enter fullscreen mode Exit fullscreen mode

Sparrowdo 控制台客户端支持许多选项，请遵循[文档](https://github.com/melezhik/sparrowdo#sparrowdo-client-command-line-parameters)获取详细信息。

最少的选项是正在部署的远程主机，或者如果我们在本地主机上运行 deploy，则为`--localhost_mode`。

# 应用源代码

这是一个简单的 Mojolicios [应用程序](https://github.com/melezhik/my-app)，它有一个端点，如果用户成功通过身份验证，它将返回“hello world”:

```
#!/usr/bin/perl

use Mojolicious::Lite;
use strict;

plugin 'digest_auth', 
  realm   => 'app',
  expires => 120,
  allow   => '.htdigest'
;

get '/' => sub {
  my $c = shift;
  return unless $c->digest_auth;
  $c->render( text => 'hello world' );
};

app->start; 
```

Enter fullscreen mode Exit fullscreen mode

# 专用系统用户

我们将保存所有的数据，并在一个专门的系统用户下运行我们的应用程序，所以让我们创建它:

```
user "web-application"; 
```

Enter fullscreen mode Exit fullscreen mode

# 从单片机获取源代码

很简单，只要加上这个:

```
git-scm 'git@github.com:melezhik/my-app.git', %( 
  to   => '/home/web-application/projects', 
  user => "web-application"
); 
```

Enter fullscreen mode Exit fullscreen mode

为了完成这项工作，我们还需要创建一个目录`~/projects`，所以让我们预先添加这一行:

```
directory "/home/web-application/projects", %(
    owner => "web-application",
    group => "web-application"
) 
```

Enter fullscreen mode Exit fullscreen mode

# 安装依赖项

所有的依赖项都是通过 cpanfile 声明的:

```
$ cat cpanfile
requires 'Mojolicious::Lite';
requires 'Mojolicious::Plugin::DigestAuth'; 
```

Enter fullscreen mode Exit fullscreen mode

所以只要运行一个纸箱安装程序:

```
bash "cd /home/web-application/projects/my-app && carton", %(
  description => "update cpan dependencies by carton",
  user => "web-application"
); 
```

Enter fullscreen mode Exit fullscreen mode

将所有声明的 CPAN 依赖项安装到`./local`目录中。

# 用密码填充文件

我们在应用程序中使用摘要式访问认证。这一点，首先我们需要创建一个带有凭证的本地文件:

```
$ htdigest -c .htdigest $realm $username 
```

Enter fullscreen mode Exit fullscreen mode

其中$real - authentication realm 是特定于应用程序的，为登录`user`创建登录/密码条目的真正命令是:

```
$ htdigest -c .htdigest app user # let's set password as `1` 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记对 Git“隐藏”`.htdigest`，所以不要偶尔提交:

```
$ echo .htdigest >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

剩下要做的就是通过 sparrowdo，
将密码文件填充到远程服务器，我们还必须将该文件存储在应用程序根目录下:

```
file '/home/web-application/projects/.htdigest', %( 
  content => slurp '.htdigest', 
  owner => 'web-application', 
  group => 'web-application' 
); 
```

Enter fullscreen mode Exit fullscreen mode

我们几乎都准备好了，我们需要做的最后一件事是设置系统服务

# 设置 systemd 脚本并运行应用服务

使用 sparrowdo，只需添加几行代码:

```
systemd-service "my-app", %(
  user => "my-app",
  workdir => "/home/web-application/projects/my-app",
  command => "/bin/bash --login -c 'cd /home/web-application/projects/my-app && carton exec ./app.pl daemon --listen http://0.0.0.0:3000'"
);

service-restart "my-app"; 
```

Enter fullscreen mode Exit fullscreen mode

该代码确保我们的应用程序可以作为 0.0.0.0:3000 访问。

# 让我们试一试

以下是本地主机部署的 sparrowdo 报告示例:

```
$ sparrowdo --local_mode  --format=production 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] create user web-application OK
push [task] create directory /home/web-application/projects OK
push [task] fetch from git source: https://github.com/melezhik ... OK
push [task] create file /home/web-application/projects/my-app/.htdigest OK
push [task] update cpan dependencies by carton ... OK
push [task] create template /etc/systemd/system/my-app.service OK
push [task] restart service my-app OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//opt/sparrow/task-box.json - OK
public@user is uptodate (0.2.1)
public@directory is uptodate (0.1.5)
public@bash is uptodate (0.1.7)
public@file is uptodate (0.0.6)
public@templater is uptodate (0.0.11)
Installing modules using /opt/sparrow/plugins/public/templater/cpanfile
Complete! Modules were installed into /opt/sparrow/plugins/public/templater/local
public@service is uptodate (0.1.15)
running task box from /opt/sparrow/sparrow-cache/task-box.json ... 
2017-11-14 16:11:03 : [task] create user web-application [path] modules/change/
2017-11-14 16:11:03 : [task] create directory /home/web-application/projects [path] modules/create/
2017-11-14 16:11:03 : [task] fetch from git source: https://github.com/melezhik ... [path] modules/bash-command/ [params] envvars:
2017-11-14 16:11:04 : [task] create file /home/web-application/projects/my-app/.htdigest [path] /
2017-11-14 16:11:05 : [task] update cpan dependencies by carton ... [path] modules/bash-command/ [params] envvars:
2017-11-14 16:11:05 : [task] create template /etc/systemd/system/my-app.service [path] modules/generate-content/
2017-11-14 16:11:05 : [task] create template /etc/systemd/system/my-app.service [path] /
2017-11-14 16:11:06 : [task] restart service my-app [path] modules/stop/ [params] os:debian service:my-app
2017-11-14 16:11:06 : [task] restart service my-app [path] modules/start/ [params] os:debian service:my-app 
```

Enter fullscreen mode Exit fullscreen mode

# 检查

现在让我们检查我们的 web 应用程序，它应该是可见的进程:

```
$ sudo ps aux | grep  web-application
web-app+ 29642  0.0  0.0  17808  7180 ?        Ss   16:11   0:00 /bin/bash --login -c cd /home/web-application/projects/my-app && carton exec ./app.pl daemon --listen http://0.0.0.0:3000 
```

Enter fullscreen mode Exit fullscreen mode

并且可以通过 http:
访问

```
$ curl --digest -uuser:1 127.0.0.1:3000
hello world 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读。我希望这篇文章有用。