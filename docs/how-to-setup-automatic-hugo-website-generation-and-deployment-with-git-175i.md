# 如何用 Git 设置 Hugo 网站的自动生成和部署

> 原文：<https://dev.to/lobo_tuerto/how-to-setup-automatic-hugo-website-generation-and-deployment-with-git-175i>

假设你试过 [Hugo，](https://gohugo.io/)你喜欢它，用它做了一个网站。

现在你已经准备好部署那个网站让*全世界*看到了。

如果你能做:
不是很好吗

```
git add .
git commit -m "Updated blog content"
git push 
```

Enter fullscreen mode Exit fullscreen mode

然后**完成**吗？

好吧，如果那听起来像是你的事情；你想要的…

然后读给我的朋友听！

* * *

# 盘点

这个教程需要什么？

*   某处的公共服务器。如果你没有，你可以从 [Linode](https://www.linode.com/) 、 [DigitalOcean](https://www.digitalocean.com/) 或类似的提供商那里获得一个 **VPS** (虚拟私人服务器)。
*   可以访问`sudo`的帐户。
*   您可以从本指南的[中找到`git`。](https://dev.to/lobo_tuerto/cmo-instalar-git-en-ubuntu-285g-temp-slug-759430)
*   从本指南来看，你使用 Gitolite 来管理你的存储库。
*   一个配置好的网络服务器——我建议查看一下 [NGINX](https://www.nginx.com/) ,因为在 Ubuntu 中设置它非常容易。

让我们假设:

●包含以下内容的本地`~/.ssh/config`文件:

```
Host git-server
  # This is the IP for your VPS
  HostName 111.111.111.111
  Port 22
  # This is the remote user
  User yolo

Host web-server
  # This is the IP for your other VPS
  HostName 222.222.222.222
  Port 22
  # This is the remote user
  User webcustomer 
```

Enter fullscreen mode Exit fullscreen mode

●你用 **git** 对 Hugo 项目进行了版本控制，你可以将代码推送到远程仓库。

●存储库的名字是`yolo-web`，所以可以这样克隆:

```
git clone git@git-server:yolo-web 
```

Enter fullscreen mode Exit fullscreen mode

# 安装依赖项

首先，让我们登录远程机器；您要在其上设置自动部署的服务器。

打开一个终端，SSH 进入:

```
ssh git-server 
```

Enter fullscreen mode Exit fullscreen mode

我们需要可用的`hugo`命令，因为最简单的方法是使用`snapd` :

```
sudo apt install snapd
sudo snap install hugo 
```

Enter fullscreen mode Exit fullscreen mode

以`git`用户的身份登录并验证`hugo`正在工作:

```
sudo su git -l
hugo version 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似这样的内容:

```
Hugo Static Site Generator v0.34 linux/amd64 BuildDate: 
```

Enter fullscreen mode Exit fullscreen mode

# 创建并配置`post-receive`钩子脚本

这是重要的部分。

这个脚本将负责在存储库接收到一个`git push`之后做**一些事情**。

在我们的例子中，我们希望脚本将当前的`master`分支的副本`git checkout`放在我们选择的目录中，然后在其上调用`hugo`来为 web 服务器生成 HTML 文件。

让我们创建`post-receive`文件:

```
touch ~/repositories/yolo-web.git/hooks/post-receive
chmod 700 ~/repositories/yolo-web.git/hooks/post-receive 
```

Enter fullscreen mode Exit fullscreen mode

并把这个内容放在里面:

```
#!/bin/bash

export GIT_WORK_TREE=/home/git/yolo-web-compiled

echo `pwd`
echo "Generating site with Hugo for yolo-web"

mkdir -p $GIT_WORK_TREE
chmod 755 $GIT_WORK_TREE

git checkout -f master

rm -rf $GIT_WORK_TREE/public
cd $GIT_WORK_TREE && /snap/bin/hugo

find $GIT_WORK_TREE/public -type f -print | xargs -d '\n' chmod 644
find $GIT_WORK_TREE/public -type d -print | xargs -d '\n' chmod 755

echo "All done! 0 problems" 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经有了一个将 Hugo 生成的最新 HTML 文件保存在`/home/git/yolo-web-compiled`中的过程。

继续，尝试一下，做一些局部的改变，`git commit`和`git push`，然后 SSH 进去，看看里面有什么`/home/git/yolo-web-compiled`。

# web 服务器情况

## 编译和 web 服务使用同一服务器

如果你的 web 服务器和你的 repos 在同一台机器上，那么你可以简单地用一个*符号链接*指向`/home/git/yolo-web-compiled/public`目录，然后收工！

否则…

## 在一台服务器上编译，在另一台服务器上 web 服务

如果您有不同的机器来托管您的 Git 存储库和 web 页面，那会怎么样呢？

这怎么可能呢？很高兴你问了...

* * *

以下是对这个问题的一个自以为是的解决方案。

●用一个空密码短语为 **git@git-server** 用户生成一个 SSH 密钥对:

```
ssh git-server
sudo su git -l
sshkey-gen -t rsa -b 4096 -C "git@git-server" 
```

Enter fullscreen mode Exit fullscreen mode

●尝试通过 SSH 访问服务器——会失败，但我们需要这样做，以便它的*签名*被注册到`~/.ssh/know_hosts`文件中。

```
ssh webcustomer@222.222.222.222 
```

Enter fullscreen mode Exit fullscreen mode

你应该回答`yes` -不要只输入`y` -当它问:

```
Are you sure you want to continue connecting (yes/no)? 
```

Enter fullscreen mode Exit fullscreen mode

● `exit`这个 **git** 用户 shell，你现在在你的 **yolo** shell 中。

●将公钥从 **git** 复制到您的家:

```
sudo cp /home/git/.ssh/id_rsa.pub ~/git.pub 
```

Enter fullscreen mode Exit fullscreen mode

● `exit`远程会话，你现在在你的**本地**机器上。

●将`git.pub`文件复制到您的本地机器:

```
mkdir ~/tmp
scp git-server:git.pub ~/tmp/ 
```

Enter fullscreen mode Exit fullscreen mode

●将密钥上传到将要托管页面的服务器:

```
scp ~/tmp/git.pub web-server: 
```

Enter fullscreen mode Exit fullscreen mode

●登录网络服务器:

```
ssh web-server 
```

Enter fullscreen mode Exit fullscreen mode

●将`~/git.pub`的内容复制到`~/.ssh/authorized_keys`的末尾。

就这样，宋承宪进入`git-server`，然后进入`sudo su git -l`，测试`ssh webcustomer@222.222.222.222`是否工作。

如果上次 SSH 登录成功，你现在可以将`scp`或`rsync`内容从**储存库机器**上传到**网络服务器机器！**

●修改`~/repositories/yolo-web.git/hooks/post-receive`文件，包括对`rsync`或`scp`的调用，将文件从`git-server`传输到`web-server`——我更喜欢使用`rsync` :

```
#!/bin/bash

export GIT_WORK_TREE=/home/git/yolo-web-compiled

echo `pwd`
echo "Generating site with Hugo for yolo-web"

mkdir -p $GIT_WORK_TREE
chmod 755 $GIT_WORK_TREE

git checkout -f master

rm -rf $GIT_WORK_TREE/public
cd $GIT_WORK_TREE && /snap/bin/hugo

find $GIT_WORK_TREE/public -type f -print | xargs -d '\n' chmod 644
find $GIT_WORK_TREE/public -type d -print | xargs -d '\n' chmod 755

rsync -avzP $GIT_WORK_TREE/public/ \
webcustomer@222.222.222.222:/var/www/yolo-web/html

echo "All done! 0 problems" 
```

Enter fullscreen mode Exit fullscreen mode

注意:您需要调整上面的`rsync`命令的最后一个参数，以匹配您网站的文件路径。