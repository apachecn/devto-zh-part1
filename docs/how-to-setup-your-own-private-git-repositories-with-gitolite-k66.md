# 如何用 Gitolite 建立你自己的 Git 库

> 原文：<https://dev.to/lobo_tuerto/how-to-setup-your-own-private-git-repositories-with-gitolite-k66>

假设您想为一个项目/团队/公司管理您自己的私有 Git 存储库。

你有什么选择？

[GitHub](https://github.com/) 非常方便， [Bitbucket](https://bitbucket.org/) 也是如此，甚至你自己安装 [GitLab](https://about.gitlab.com/) 。

但是，有一个地方可以用更简单的方法来管理你自己的私有 Git 库，并让你的源代码完全在你的控制之下。

你这个控制狂。；)

[Gitolite](https://github.com/sitaramc/gitolite) 小巧、简单、功能强大。

用户访问控制和回购创建非常简单，只需一个`git commit` & `git push`就可以完成！

本指南将向您展示如何用 Gitolite 建立您自己的私有 Git 基础设施，以便于存储库和用户管理。

请继续阅读！

* * *

# 盘点

你需要什么样的指南？

*   某处的公共服务器。如果你没有，你可以从 [Linode](https://www.linode.com/) 、 [DigitalOcean](https://www.digitalocean.com/) 或类似的提供商那里获得一个 **VPS** (虚拟私人服务器)。
*   可以访问`sudo`的帐户。

让我们假设一个内容如下的`~/.ssh/config`:

```
Host git-server
  # This is the IP for your VPS
  HostName 123.123.123.123
  Port 22
  # This is the remote user
  User yolo 
```

Enter fullscreen mode Exit fullscreen mode

# 安装

首先，让我们登录远程机器；你想在上面安装吉托利特的那个。

打开一个终端，SSH 进入:

```
ssh git-server 
```

Enter fullscreen mode Exit fullscreen mode

然后安装`git` :

```
sudo apt install git 
```

Enter fullscreen mode Exit fullscreen mode

现在，添加将负责管理回购的用户并实施访问控制规则，然后出于安全原因禁用其密码访问:

```
sudo adduser git
sudo passwd -l git 
```

Enter fullscreen mode Exit fullscreen mode

在您的本地机器上，打开一个新的终端，并将您的**公钥**复制到远程机器:

```
scp ~/.ssh/id_rsa.pub git-server: 
```

Enter fullscreen mode Exit fullscreen mode

如果成功，您将在远程机器上的`$HOME`目录中为远程`yolo`用户提供`id_rsa.pub`文件。

* * *

是时候为 **git** 用户创建`$HOME`目录了；然后我们将把`id_rsa.pub`文件移动到`/home/git/.ssh/yolo.pub`，并调整一些权限和目录所有权:

```
sudo mkdir -p /home/git/.ssh
sudo mv ~/id_rsa.pub /home/git/.ssh/yolo.pub
sudo chmod 700 /home/git/.ssh
sudo chown -R git:git /home/git/ 
```

Enter fullscreen mode Exit fullscreen mode

让我们模拟一下 **git** 用户，然后克隆并安装 Gitolite:

```
sudo su git -l
git clone https://github.com/sitaramc/gitolite ~/gitolite
mkdir ~/bin
~/gitolite/install -to ~/bin 
```

Enter fullscreen mode Exit fullscreen mode

退出 **git** 用户 shell 并重新登录，让我们可以使用刚刚安装在`~/bin`中的命令。

然后用`yolo.pub`设置 Gitolite 作为管理员密钥:

```
exit
sudo su git -l
gitolite setup -pk ~/.ssh/yolo.pub
exit
exit 
```

Enter fullscreen mode Exit fullscreen mode

退出远程机器——您需要键入两次`exit`,因为您有两层深度( **yolo** = > **git** )。

*   **DEBIAN 笔记**

如果`~/bin`中的脚本没有被自动拾取，您可能需要创建一个`~/.bash_profile`文件，并通过将`~/bin`附加到它来修改`$PATH`，如下所示:

```
export PATH=$PATH:~/bin 
```

Enter fullscreen mode Exit fullscreen mode

**Ubuntu** 选择`~/bin`中的脚本就可以了。

现在让我们克隆`gitolite-admin`主回购:

```
git clone git@git-server:gitolite-admin ~/gitolite-admin 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以创建新的存储库，并允许用户使用他们的公共 SSH 密钥进行访问！

# 仓库和用户管理

从想要 Git 访问的用户那里获取公钥，并将它们放入`~/gitolite-admin/keydir`。

给它们起相应的名字(例如`tom.pub`和`jerry.pub`)，因为这些名字是你将要用来配置用户访问控制的。

## 新建资源库和用户权限

要创建一个名为 **yolo-project 的新存储库，**给自己做任何事情的权限，然后给*读写*访问 **tom** 和 **jerry** 的权限，打开`~/gitolite-admin/conf/gitolite.conf`文件，将这些行放入:

```
repo yolo-project
  RW+ = yolo
  RW = tom jerry 
```

Enter fullscreen mode Exit fullscreen mode

之后，您需要*提交*和*推送*这些更改到远程`gitolite-admin`回购:

```
cd ~/gitolite-admin
git add .
git commit -m "Add new repo, add new keys, give access"
git push 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，Gitolite 将负责创建新的存储库，然后根据您指定的规则控制对它的访问。

## 克隆新的回购

这很简单:

```
git clone git@git-server:yolo-project 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以向它添加内容并照常使用`git`。

## 上传一个已有的知识库

如果您已经有了一个 Git 项目，但想上传它，该怎么办？

那也很容易，`cd`进入你现有的资源库并且:

```
git remote add origin git@git-server:yolo-project 
```

Enter fullscreen mode Exit fullscreen mode

使用
进行测试

```
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

最后，推送您的*提交*，并为`master`分支:
设置跟踪

```
git push --set-upstream origin master 
```

Enter fullscreen mode Exit fullscreen mode

恭喜，我们完成了！:D

* * *

现在您已经有了一个完整的私有 Git 存储库基础设施，以及一个管理它们和它们的用户的简单方法。

这只是一个简单的`git commit` & `git push`之遥！

## 访问控制规则信息

如果你想知道你有哪些回购，试试这个:

```
ssh git@git-server info 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

*   [添加用户和回购](https://github.com/sitaramc/gitolite#adding-users-and-repos)
*   [访问规则示例](https://github.com/sitaramc/gitolite#access-rule-examples)