# git 部署

> 原文：<https://dev.to/scottrobertson/git-deployments-n1>

在过去的一年左右的时间里，我一直使用 Rsync 来部署我的站点，直到最近，它对我来说工作得非常好。

我最近在服务器上使用了一个简单的 post-receive 钩子。我发现这对我来说更好，因为这意味着我需要做的就是“推”。

首先，您需要在您的服务器上设置 repo，例如我通常在 **/var/git/site.com** 中设置它。这将是一个裸 git 回购，所以它不会包含任何您自己的代码。要设置它，只需在你的 **/var/git/site.com** 文件夹中运行以下命令:

```
git init --bare 
```

Enter fullscreen mode Exit fullscreen mode

这将初始化存储库，允许您将代码推送到其中。

下一步是告诉 git 在哪里检查您的代码。我一般用 **/var/www/site.com** 。为此，我使用了一个非常简单的 post-receive 钩子，将代码签出到文件夹中，并运行 **composer install** 。你可以把下面的内容放到**/var/git/site . com/hooks/post-receive**:

```
WORKING_DIR=/var/www/site.com;
GIT_WORK_TREE=$WORKING_DIR git checkout -f;
cd $WORKING_DIR && composer install -o; 
```

Enter fullscreen mode Exit fullscreen mode

需要记住的一点是，通过运行以下命令使文件可执行:

```
chmod +x /var/git/site.com/hooks/post-receive 
```

Enter fullscreen mode Exit fullscreen mode

既然已经设置了 repo 和 post-receive 挂钩，我们需要设置您的开发机器的 git 远程。最简单的方法是:

```
git remote add production user@site.com:/var/git/site.com 
```

Enter fullscreen mode Exit fullscreen mode

这意味着您可以简单地运行以下命令来部署您的网站:

```
git push production master 
```

Enter fullscreen mode Exit fullscreen mode

很明显，你可以在接收后钩子上添加任何你想添加的东西，比如清除缓存，刷新 cdn 等等。我目前已经将我的大部分设置为在 **composer 安装**之后重启 php-fpm。

```
service php5-fpm restart 
```

Enter fullscreen mode Exit fullscreen mode

如果你有任何问题，或者我错过了什么，请在下面告诉我。