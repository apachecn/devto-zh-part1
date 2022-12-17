# 如何为 web 开发代码推送建立 git 裸存储库

> 原文：<https://dev.to/coolgoose/how-to-set-up-a-git-bare-repository-for-web-development-code-pushes-5ca4>

大家好，让我们考虑一下，您已经有了一个很好的本地开发环境，您可以在其中开发项目。在某个时候，你会想把这个项目放到一个公共服务器上，与朋友、客户或者顾客分享。当然，由于您使用的是版本控制，您需要一种简单的方式将更新推送到服务器。

一个简单的方法是在你的服务器上设置一个 git bare repo。关于这些差异的详细解释，你可以看看这篇文章。

既然我们已经解决了这个问题，那就让我们进入正题吧。
让我们假设我们的公共文件夹在`/home/~username/public`处，其中`~username`是系统用户。
我通常做的是首先设置我的公钥，作为进入服务器的一种方式。

```
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 644 .ssh/authorized_keys 
```

Enter fullscreen mode Exit fullscreen mode

现在使用 vim 或您喜欢的编辑器将公钥添加到`authorized_keys`中。

下一步是启动我们的 git 裸存储库。根据个人习惯，我创建了一个`.gitrepo`文件夹，所以如果更适合你，你可以随意使用其他的。唯一重要的约束是**而不是**在公共文件夹中创建它。

```
mkdir .gitrepo
cd .gitrepo/
git init --bare
cd hooks/
touch post-receive
chmod +x post-receive 
```

Enter fullscreen mode Exit fullscreen mode

现在就是写一个做工作的小 shell 脚本的问题了。

我们有两个选项来设置结帐。

1.  仅签出特定的分支

```
#!/bin/sh
GIT_WORK_TREE=../ git checkout -f [branch] 
```

Enter fullscreen mode Exit fullscreen mode

1.  检查任何分支(这对开发设置很有用)

```
#!/bin/sh
while read oldrev newrev refdo
    branch=´echo $ref | cut -d/ -f3´
    GIT_WORK_TREE=../ git checkout -f $branch
done. 
```

Enter fullscreen mode Exit fullscreen mode

因为我们有了接收后挂钩设置，所以我们还可以运行任何其他所需的 shell 命令，例如编译我们的 web 资产或安装所需的依赖项。

一旦完成，您就可以在使用下面的命令将远程存储库添加到本地 git 存储库之后使用它:

```
git remote add [remote-name] ssh://[~username]@[site|ip]/~/.gitrepo 
```

Enter fullscreen mode Exit fullscreen mode