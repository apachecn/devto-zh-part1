# Mac 上本地 WordPress 开发的终极指南

> 原文：<https://dev.to/davidgagne/the-ultimate-guide-to-local-wordpress-development-on-a-mac>

我知道这是个大胆的标题。但是我已经为此努力了五年，现在终于对我的配置非常满意了。如果你想在本地机器上使用 WordPress 主题或插件，用 GitHub 管理你的代码库，并毫不费力地部署更新，你来对地方了。

我将这篇文章从[我的个人网站](https://www.davidgagne.net/2017/01/23/the-ultimate-guide-to-local-wordpress-development-on-a-mac/)交叉发布到 dev.to，因为:
(a)我希望它对任何试图顺利完成这一切的人都有用,
(b)我真诚地希望其他人能给我一些反馈，他们可能会指出我做得非常低效的事情，和/或告诉我这篇文章中是否有任何部分是不正确的。

我们走吧！

### 本地设置

你需要做的第一件事就是升级到 Mac OS X Sierra 10.12.2，否则你就会左右为难。不过，您应该已经这样做了，因为您是一名开发人员，而开发人员不喜欢陈旧的操作系统。

在那之后，你需要遵循 Chris Mallinson 的优秀教程，[为你的新 Mac 电脑提供完美的网络开发环境](https://mallinson.ca/osx-web-development/)。(我不打算在这里重复他的整篇文章，因为他做得太棒了。)如果您愿意，可以跳过最后关于实现自定义主页的部分。我没有这样做，如果你已经有了自己的导航文件系统的方法，这并不重要。我使用 Coda 作为我的 IDE，它负责组织我的项目。

Chris 的说明中唯一棘手的部分是正确获取 httpd.conf 文件。你可能会很幸运，只要遵循他的规则，一切都会很完美。我并不幸运，不得不做了一些修改，但这可能是因为我多年来一直在修改我的 httpd.conf 文件。如果你联系我，我很乐意给你发送我的 httpd.conf 和 httpd-vhosts.conf 的副本，它们可能会在你的机器上工作。我在 iCloud Drive 文件夹中保存了它们的档案，并在多台机器上使用完全相同的配置——两台 Mac Mini 台式机和我的 MacBook Air——没有任何问题。

在本地安装了 [MySQL](http://dev.mysql.com/downloads/mysql/) 之后，你需要确保你有能力实际使用它。 **Terminal.app** 是你的朋友:

```
/usr/local/mysql/bin/mysqladmin -u root -p'temppassword' password 'newpassword'

```

现在你可以为 WordPress 创建一个数据库。我总是把我的命名为“wordpress ”,但是你真的可以把它命名为任何东西。

在这段代码中， *temppassword* 是 MySQL 安装程序为 root 用户提供的。对*的新密码*再次使用同一个密码可能会非常复杂，但如果你愿意，你可以修改它。

一旦你开始工作，你就可以开始真正的烹饪了。下载一份 [WordPress](https://wordpress.org/download/) 的拷贝，解压后先放入~/Sites/wordpress/中。您可能会遇到一些权限问题，但是您可以使用以下方法来处理它们:

```
sudo chown -R _www ~/Sites/wordpress ; sudo chmod -R g+w ~/Sites/wordpress

```

您需要确保您可以通过在您的 wp-config.php 脚本中添加这一行来[在本地](https://wpfortune.com/blog/wordpress-tips-tricks/install-plugins-localhost/)安装插件:

```
define('FS_METHOD', 'direct');

```

此时，你应该能够点击 [wordpress.dev](wordpress.dev) 并开始 wordpress 的安装过程。我知道使用 localhost 作为数据库主机很诱人，但是您应该坚持使用 127.0.0.1。数据库*名称*将是您几分钟前添加到 MySQL 的数据库。

### GitHub 设置

好吧。所以现在你有了在本地运行的 WordPress，你可以编辑主题和插件，而不用担心在你黑的时候关闭一个生产网站。现在是认真的时候了。你有 GitHub 桌面吗？如果没有，就抢过来安装。(如果你是某个疯狂的人，通过 <abbr title="command line interface">CLI</abbr> 做*每件事*，我不知道你为什么还在读这篇文章，因为你可能已经知道怎么做了。)

先做主题开发吧。创建一个新的 [GitHub](https://github.com/) 库，并用一个自述文件初始化它。我假设你把你的回购命名为“天行者”，因为你是一个了不起的人，这就是了不起的人做的。(公不公，私不私，由你决定。)点击绿色大**克隆或下载**按钮，选择“在桌面打开”。你将在~/Sites/WordPress/WP-content/themes/Skywalker 中保存天行者，这应该只需要亿万分之一秒，因为它现在是一个空的回购。

好吧。编辑你的主题。你不必做任何戏剧性的事情；只需放入一个 style.css 文件，并添加一个标题即可开始并保存它。切换到您的 GitHub 桌面应用程序，它应该显示一个文件已经被更改，并准备提交。但是先不要承诺。我们才刚开始有趣的部分。

在您做任何事情之前，SSH 进入您的 web 服务器。如果你不能通过 SSH 连接到你的网络服务器，你就不走运了，你真的不能继续了。不过，您应该能够 SSH 到您的 web 服务器。如果不能，告诉他们您需要 shell 访问，否则您将切换到不同的主机！(我推荐 [DreamHost](https://www.dreamhost.com/r.cgi?8624) 。)一旦连接到您的服务器，运行以下命令确保它安装了 git:

```
which git

```

如果您的 web 服务器没有运行 git，猜猜会发生什么？你需要一个更好的网络主机。

准备好了吗？好的……继续。

下载 Marko Markovi 的[简单 PHP Git 部署脚本](https://github.com/markomarkovic/simple-php-git-deploy)并更新其配置脚本。

*   您可以从方便的[随机密钥生成器](http://randomkeygen.com/)中获得 SECRET_ACCESS_TOKEN 的值。我知道您想使用一个强密码或 Fort Knox 密码，但是这个密钥需要作为 querystring 变量使用，所以省点麻烦，使用 CodeIgniter 加密密钥就可以了。如果你正在为政府或银行开发一个 WordPress 主题，那就要想方设法确保它的安全。否则我不会太担心有人通过破解这里的代码来入侵当地披萨店的网站。
*   将 REMOTE_REPOSITORY 更新为您自己的版本。这将看起来像 https://github.com/YOUR_GITHUB_USERNAME/skywalker.git
*   现在让分支设置为 master。如果你在这里转动你的眼睛，并且知道你想要使用一个不同的分支，发疯吧。这应该是给初学者的教程。
*   你的 TARGET_DIR 将依赖于你的 web 主机，所以我不能在这里给你太多的指导。如果你使用 [DreamHost](https://www.dreamhost.com/r.cgi?8624) 并在你的域的根目录下运行 WordPress，它可能看起来像这样:*/home/DreamHost _ USERNAME/your website . com/WP-content/themes/Skywalker*

您需要通过 FTP——或者更好的是 sFTP——将这两个文件传输到 web 服务器的某个地方。在哪里并不重要，只要外部世界可以访问它们。

您还需要来自 web 服务器的公共 SSH 密钥(通常是 id_rsa.pub)的内容。SSH 进入您的服务器，如果您无法从 web 主机获得密钥，请运行以下命令来生成密钥:

```
ssh-keygen -t rsa

```

这样做可能会将 id_rsa.pub 放入一个/。ssh/ folder。(可以用 **vi** 来达到。你只需要复制它的内容。)

当你仍然连接到你的网络服务器时，试着从它连接到 GitHub 以确保你能做到。您也需要这样做来“授权”您的 web 服务器连接到 GitHub。运行以下命令:

```
ssh git@github.com

```

你应该得到一个类似这样的回答，“这很有效，但是你不能这么做，”这有点让人困惑，但是没关系。如果不起作用，你需要和你的网络主机和/或 GitHub 上的人谈谈，找出为什么你的服务器不能和 GitHub 对话。我假设它有效，这样你就可以进入下一步了。

回到 GitHub，点击你的库的*设置*标签，进入*部署键*面板。您将添加刚刚创建的 **id_rsa.pub** 文件的内容作为部署密钥。(你可以随便给它起什么名字，据我所知名字并不重要。)但是，这里有一个问题:您只能在一个存储库上使用一个 deploy 键。因此，如果您想在同一服务器上处理两个独立的主题，您需要在您的服务器上为每个主题生成一个新用户，然后为每个用户生成一个新的 SSH 密钥，或者您需要将整个 wp-content 目录放到 repo 中。(我就是这么做的，但我不建议这么做，因为这样你就必须处理 git——忽略大量文件，因为你所有的 WordPress 媒体上传和任何其他插件也在那个文件夹里。)

切换到 *Webhooks* 面板，添加一个新的 webhook。

*   您的“有效负载 URL”是您上传的 deploy.php 脚本的完整路径，以及包含您添加到 deploy-config.php 脚本的 SECRET_ACCESS_TOKEN 的查询字符串。只是追加？sat=SECRET_ACCESS_TOKEN 到文件。
*   “内容类型”应该是应用程序/json
*   我将我的 webhooks 设置为“Send me everything”，尽管如果你想最小化它被 pinged 的频率，你可以将它限制为仅 push 事件。
*   是的，你想激活它。

当你完成后，点击绿色的“添加 webhook”按钮，GitHub 将发送一个测试 ping 到你的 web 服务器的 deploy.php 脚本，并告诉你结果。

### 包扎

如果到目前为止一切顺利，您应该能够切换回 GitHub 桌面应用程序并运行 commit。(我将我的设置为总是提交和同步。在“编辑”下，选中“提交后自动同步”选项。)当您提交时，您的文件被推送到 GitHub，GitHub 触发 webhook，web hook 调用部署脚本，部署脚本从您的 repo 中提取您的文件，并将它们推送到您的 web 服务器，然后**嘣！**，现在你的新天行者主题正在制作中。多酷啊。！

### 奖励积分

您可以为存储库的不同分支使用不同的 webhooks。因此，假设您有三个分支:开发、测试和主。在您的 web 服务器上，您将拥有 deploy-dev.php、deploy-beta.php 和 deploy.php，每个都有自己唯一的 SECRET_ACCESS_TOKEN，并且每个都使用不同的 TARGET_DIR 访问自己唯一的配置文件，更新相应的文件夹。既然你可以[有选择地分配权限给分支](https://help.github.com/articles/about-branch-restrictions/)——假设你有一个“Organization”GitHub 账户——这意味着你可以让一些开发者在 dev 分支上工作，并且只允许推送你的 dev 文件夹。然后你让你的 dev 文件夹服务于你的 dev 子域，现在你不必担心开发人员不小心把一些东西投入生产。

如果你使用 [Slack](https://slack.com/) 你可以配置一个不同的 webhook，在有人提交的任何时候向 Slack #channel 发送消息，如果你正在处理一个大项目，这是很方便的。

### 资源:

*   [适合您新 Mac 的完美网络开发环境](https://mallinson.ca/osx-web-development/)
*   [在 Mac OS X 上安装 WordPress](https://coolestguidesontheplanet.com/fastest-way-to-install-wordpress-on-osx-10-6/)
*   [让 Apache、MySQL、PHP 和 phpMyAdmin 在 OSX 10.11 El Capitan 上工作](https://coolestguidesontheplanet.com/get-apache-mysql-php-and-phpmyadmin-working-on-osx-10-11-el-capitan/)
*   [MySQL 下载页面](http://dev.mysql.com/downloads/mysql/)
*   [WordPress 下载页面](https://wordpress.org/download/)
*   [如何在本地主机上安装插件](https://wpfortune.com/blog/wordpress-tips-tricks/install-plugins-localhost/)
*   [GitHub](https://github.com/)
*   [GitHub 桌面](https://desktop.github.com/)
*   [简单 PHP Git 部署脚本](https://github.com/markomarkovic/simple-php-git-deploy)