# 创建、设置并部署到您自己的云 web 服务器。

> 原文：<https://dev.to/davedodea/create-setup-and-deploy-to-your-own-cloud-web-server-ph>

[![](img/e9c5c25b9221adff5a165bb8e83edca5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tKKJjlJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A-QWxPhugmlBm79nuBawfZw.png)

当你想让你在[自由代码营](http://freecodecamp.com)学到的一切对这个糟糕的世界可见的时候！

***注:*** *这是本* [*系列*](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a) *的一个完整的、一体化的版本，故名其长。*

让我们将任务分成 5 个小部分:

1.  *创建服务器* ***←你来了！***

*2。* [*设置服务器。*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)

*3。* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

*4。* [*设置一个域名(例如 www.myDomain.com)。*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3)

*5。* [*获取服务器上的文件。*](https://medium.com/@davedodea/how-to-deploy-my-website-to-a-server-b9d63da5fa34)

### 为什么？

对我来说，建立自己的网络服务器的决定可以归结为三个原因。价格:

*   正如您将看到的，购买 VPS(虚拟专用服务器)的成本只是托管服务的一小部分。

1.  控制:

*   设置您自己的服务器允许您控制服务器的所有方面及其操作方式，一直到命令行级别。

1.  学习:

*   如果你正在阅读这篇文章，这意味着你很可能对学习更多关于 web 开发的知识感兴趣。服务器是这项技术不可或缺的一部分，因此，建立和运行你自己的网络服务器将为你提供无价的技能。

### 如何？

[![](img/5d184ec156671a43d0b4cda75a058d15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MRrKWFJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/354/1%2A97w3CB0GxLVAy1wl1Gw9lQ.png) 

<figcaption>[数字海洋。](https://digitalocean.com)</figcaption>

*   你将使用一种叫做数字海洋的服务来托管我们的服务器。DigitalOcean 为从基本服务器到高内存计算引擎的所有产品提供极具竞争力的价格。
*   他们每月 5 美元的 droplet(用数字海洋的说法，droplet =服务器)足够满足我们的需求了。它配备了 512mb 内存和 20Gb 固态硬盘存储。
*   使用[此处的链接](https://m.do.co/c/5ff60a2d6040)，您可以获得 10 美元(两个月免费)。

一旦您使用上面的链接在 DO (DigitalOcean)上创建了帐户，请转到您的仪表盘，点击绿色的**创建 Droplet** 按钮。

[![](img/598b3f85a433e9314e6e8d17ec5b99db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vA2nVMp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7ov0n53u7UtjuRdjLTJ7dQ.png)

*   在下一页上，选择以下选项，如我在图像中所示。您将使用最新、最稳定的 Ubuntu server 版本作为我们的操作系统。

[![](img/71dd06e3c238d58944712659b6212a44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ts52NjR0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5beZumJMrzCQLBC0tHgAvA.png)

*   现在让我们忽略**块存储**选项(稍后我会写一篇关于这个的文章)。
*   选择地理上离您最近的**地区**:

[![](img/3c7ee57deb2fd4406af424a6627cbe47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R6LSizyt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0ZnHmYoUEFfTtIcp294U6w.png)

*   此时无需选择任何**附加选项**。
*   现在，关于 SSH 的下一步是为您的服务器提供额外的认证安全层。我建议设置这个，但是，我认为这超出了这篇介绍文章的范围，应该有一个专门的帖子，所以我将在以后讨论这个问题。现在，不要选中这些选项。
*   继续选择您需要多少台服务器，现在 1 台就够了。然后，将您的服务器命名为例如“myFirstServer”或任何您喜欢的名称。_

[![](img/da484822e9a63a90b413fd8dc69add86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hulfWXkC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/855/1%2AQjiDIT8Do9RLw0zcm3POOA.png)

*   点击**创建**后，不到一分钟的时间就能为您构建好您的服务器——太棒了！

[![](img/4e0bbf0bd45636c46b35b648288f0243.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q0Mlexhi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/815/1%2AagFH9hAlr6-XFKVrE_J5GQ.png)

*   不久之后，您将收到一封电子邮件，其中包含新服务器的凭据。

[![](img/59737f91e1f69a514680e0b33f7b23f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_iXk7l02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/473/1%2AdT3DRr97vrauDHMc56LC1g.png)

..就是这样。干得好！你刚刚在 DigitalOcean 平台上创建了一个新的虚拟私人服务器或 Droplet 拍拍自己的背。

*接下来，您将在这里通过远程登录、设置用户和完成一些基本的安全步骤来设置您的新服务器！*

### 第一次设置新的 Ubuntu 服务器。

[![](img/31123953652d6b30b77a264d6f743ac8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V_kkfceU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/540/1%2AlJOKfP8JH4yn2xLWqMNapw.png)

这是“[如何建立自己的基于云的 web 服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)”的第 2 部分

1.  [*创建服务器。*T3】](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)

*2。设置服务器* ***←你来了！***

*3。* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

*4。* [*设置一个域名(例如 www.myDomain.com)。*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3)

*5。* [*获取服务器上的文件。*](https://medium.com/@davedodea/how-to-deploy-my-website-to-a-server-b9d63da5fa34)

那么，现在你已经在[数字海洋](https://m.do.co/c/5ff60a2d6040)上[创建了你的服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)或 droplet 让我们继续进行一些基本的设置步骤，为第一次启动我们的网站做准备！

在您在上面的步骤 1 中创建了我们的 droplet 之后，您将会通过电子邮件收到一些凭证，其中包括我们的 droplet 的名称、IP 地址和密码等详细信息。

为了连接到您的服务器，您需要使用[SSH](https://en.wikipedia.org/wiki/Secure_Shell)——一种允许您远程登录到新服务器命令行的协议。

> MacOS 设备(我使用的)都有一个名为*终端*的内置应用程序，它提供本地 SSH 功能，你可以在你的应用程序目录中找到。> **Windows 用户**，可惜，没这么奢侈。所以有几个选择，其中最流行的是 PUTTY——一个用于 Windows 的 SSH 和 TELNET 客户端。你可以阅读更多关于获取 [PUTTY 设置 he_re_](https://mediatemple.net/community/products/dv/204404604/using-ssh-in-putty-) *。* > *其余的截图将来自我的 Mac，但步骤是相同的，一旦你使用 PUTTY 登录到你的服务器。*

好了，让我们开始发出一些命令(我把我的服务器 IP 留在了可见的地方，因为写完这篇文章后我会销毁它，但出于安全考虑，我建议你不要在网上分享它):

1.  要连接到您的服务器:

```
ssh root@yourServersIP 
```

您将看到类似的内容，键入' *yes '* :

[![](img/353381bd4504cee1be152d9a2d7c1193.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pY0RsoJR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/733/1%2ACzDb7ts2TxkcSj1yO0J5FA.png)

在输入“是”之后，你应该粘贴你的密码，这不会出现在屏幕上，所以不要担心，只需粘贴(CMD + V)并按回车键。

然后，您将登录到您的服务器，并看到以下内容:

[![](img/baf8e561334106ff24d5001fbd037aba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6GpaXp6b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/805/1%2AgLzil4VZ4sfMlcY3olz03w.png)

再次粘贴您的密码，按 return 键，系统会提示您输入新密码并进行确认:

[![](img/cd6f57e75690f9b8e7026810a1328ea1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8RdHq2Ki--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/771/1%2Ayzbm8bP5pzqmysYzleBgWA.png)

太好了，你现在登录了，让我们开始吧！

1.  当前用户 root 拥有不受限制的特权，可以在我们的系统上为所欲为。您希望通过创建新用户来限制这种潜在的灾难。

在我们的命令行中，让我们键入:

```
adduser newUser 
```

您可以将这个新用户添加到拥有管理员权限的用户组中。该组中的用户可以在输入管理员密码后执行受限任务。

```
usermod -aG sudo newUser 
```

1.  暂时不要关闭当前连接，而是在命令行中打开一个新的选项卡，并尝试使用以下内容登录:

```
ssh newUser@yourServerIP 
```

如果能登录，太好了！—如果没有，请返回上面的步骤 2。

完成上述步骤后，您现在可以作为新用户而不是 root 用户登录。

现在，这将是一个很好的时间来加强我们的安全性，并在我们的本地机器和服务器上添加 SSH 密钥对，我将在未来关于服务器安全性的帖子中涉及这一点，这也将包括其他主题，如禁用 root 登录，设置防火墙等。—为了让这篇帖子保持简洁。

…太棒了，如果您没有成功远程登录到您的服务器，请设置一个新用户。

*接下来，您将体验如何安装 LAMP stack，这项技术将为我们的网站访问者提供网页服务！*

### 如何在新服务器上安装灯栈。

[![](img/f7b412f917f3132ab8050af399cb55cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5PAM5k7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/492/1%2ATR4kKlINtbPDrQ3ffyem0g.png)

这是“[如何建立自己的基于云的 web 服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)”的第 3 部分

1.  [*创建服务器。*T3】](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)

*2。* [*设置服务器。*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)

*3。安装灯栈* ***←你来了！***

*4。* [*设置一个域名(例如 www.myDomain.com)。*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3)

*5。* [*获取服务器上的文件。*](https://medium.com/@davedodea/how-to-deploy-my-website-to-a-server-b9d63da5fa34)

好的，到目前为止你取得了很大的进步。你已经[创建了一个服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)并且完成了一些[的基本设置任务](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)。

现在，一旦你上传了我们的代码文件——HTML、CSS、JavaScript 等等，服务器怎么知道如何处理它们？我的意思是，目前，你有一个基本的 Ubuntu Linux 服务器，它可以用于任何事情(几乎)，它不是为了服务你如此热爱开发的网页而设置的！

为了创造这种神奇的效果，你需要使用一个叫做 LAMP 的开源软件栈(一组协同工作的技术), LAMP 代表:

*   **L** inux:操作系统。
*   Apache:web 服务器软件。
*   ySQL:数据库。
*   惠普:提供动态内容。

这将不是一个关于上述每个主题的深入课程，而是更多的关于如何让他们一起工作，以服务于我们的网站。

让我们开始吧:

1.  **阿帕奇:**

一旦[登录到我们的服务器](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)，您就可以开始发出一些命令，首先让我们更新我们的服务器，然后安装 Apache:

```
sudo apt-get update

sudo apt-get install apache2 
```

您将看到类似于以下内容的内容:

[![](img/a4412d3d4257b7eabe0cef4a6304b7af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DaHN_pxN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqBOyMb63tELewxUg2TL2Hg.png)

输入 yes，按回车键，Apache 就会安装。

在这个阶段，您可以通过访问服务器的 IP 地址来验证 Apache 是否安装正确，您应该看到:

[![](img/f506c47b66beaf811471972f3ac73a35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rN6zwtnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGRMouL5-rmLG8X60Q8yBAg.png)

显示您的*公共* IP 的快速方法是:

```
curl ipinfo.io/ip 
```

**2。MySQL:t1 版**

让我们开始吧:

```
sudo apt-get install mysql-server 
```

你会看到:

[![](img/458a04f3b6df6efec1158e237c8af1b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dos_pT3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7NUf6sCcmk8PGgwTp29gkw.png)

你可以回答是，然后按回车键。在这些包被拉进来之后，你会被要求输入一个 MySQL 管理员密码。输入密码，然后确认:

[![](img/b24440845fe8ea11d3cfea1eaea57f4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pZif9Y5G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwA9sbbaVx5vlt2_jX4El_A.png)

MySQL 将继续安装。

完成后，您需要创建一个数据库目录，如下所示:

```
sudo mysql_install_db 
```

注意:在 5.7.6 和更高版本上，不需要此命令。如果您收到类似以下内容的错误:

```
[WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
[ERROR] The data directory needs to be specified. 
```

然后你可以运行:

```
mysqld --initialize 
```

**3。PHP**

在 Ubuntu 16.04 中，默认安装 PHP。但是，如果它没有/已被移除/已被删除，您可以通过以下方式安装:

```
sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt 
```

现在，您只需要编辑一个文件，以确保我们的 web 服务器，在这种情况下是 Apache(NginX 是您将在以后的帖子中讨论的另一个文件)，将会查找。 *php 默认*文件。

要在 linux 服务器中编辑文件，有几个选项，即 [VI/VIM](https://en.wikipedia.org/wiki/Vim_(text_editor)) 对于更有经验的人来说是一个很好的选择，因为它需要一段时间来掌握，甚至变得有效率！今天，你将使用 [Nano](https://en.wikipedia.org/wiki/Nano) ，它对初学者友好，开箱即可满足我们的需求。

因此，您将编辑一个名为 *dir.conf* 的文件:

```
sudo nano /etc/apache2/mods-enabled/dir.conf 
```

文件内容将如下所示:

[![](img/38482d37dfb33f30bd89d178b941d5cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iJSWQ17I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABc5-lB_BDkLI4lIUscR6rA.png)

这是在 Nano 文本编辑器中打开的 *dir.conf* 。您需要做的是编辑第二行，使 index.php 的*位于列表的第一位，如下所示:*

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule> 
```

要在 Nano 中保存文件，按 CTRL + X，然后在编辑器的底部要求您确认保存，键入 *Y :*

[![](img/b094b1c47cb8a158ff9697b7a54b27d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rSP1ky-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkXXGLhhSuU7R-a7Z4T7ZCg.png)

然后你会被要求确认/编辑文件名，点击回车键:

[![](img/e94281b14a83198b0e3030526187f920.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H4SOCBkq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOAbhezLcz9rtqWWVRISwFA.png)

太好了，你刚刚在服务器上编辑了一个文件，用命令行和 Nano——糟糕的屁股！

现在，剩下要做的就是重新启动 Apache 服务，以使我们所有的更改生效:

```
sudo service apache2 restart 
```

…就是这样！进步很大！你知道有灯栈安装和设置，准备服务网页。

*接下来，您将简要了解如何通过 DigitalOcean 的 DNS 部分将您新购买的域名连接到您的 droplet，以便您的访问者可以在没有 IP 地址的情况下访问您的网站！*

### 为新网站或服务器设置域名。

[![](img/bad393626205a3df455578438739a33f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--23MkesEm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/613/1%2AVW3kmLc8H5LjwVPXPhm20g.png)

这是“[如何建立自己的基于云的 web 服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)”的第 4 部分

1.  [*创建服务器。*T3】](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)

*2。* [*设置服务器。*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)

*3。* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

*4。设置一个域名(例如*【www.myDomain.com】*)***←你在这里！*T9】***

 **5。* [*获取服务器上的文件。*](https://medium.com/@davedodea/how-to-deploy-my-website-to-a-server-b9d63da5fa34)

至此，您差不多完成了这个系列。到目前为止，你已经学会了如何 c [*创建一个服务器*](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a) *，s* [*安装服务器*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866) *和 i* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

你现在有了一个响应我们服务器公共 IP 的网站。

但是，你需要做的是通过数字海洋仪表盘上的 [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) 设置将一个域名，例如【www.myDomain.com】T2 连接到我们的服务器上。

让我们陷入…

1.  **购买域名:**

我不会在这一步花太多时间，因为在使用任何知名提供商时，这个过程都相当简单。我喜欢使用[GoDaddy.com](http://godaddy.com)仅仅是因为它们便宜，而且可以直接编辑你下一步需要做的 DNS 设置。

购买域名后，登录 GoDaddy 账户，进入“管理我的域名”部分。

**2。更改 DNS 名称服务器:**

目前，我们的域名由 GoDaddy 的域名服务器管理。

DNS 域名服务器的工作原理是这样的(高级别):

*   你在浏览器中输入一个网址→
*   URL 被路由到负责所述 URL 的名称服务器→
*   名称服务器查找自己的表以查看“…嘿，谁拥有这个 URL/域”→
*   然后，它会将您的浏览器定向到与该域关联的相关服务器 IP。

考虑到这一点，我们将名称服务器更改为 DigitalOcean，因为这是我们的服务器所在的位置:

现在，选择您列出的域旁边的下拉菜单:

[![](img/6658f9740ea41fb1ab49947ba4cb5a15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lUdxFdTI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVzZrKrND8N8YELOLxvDTWQ.png)

转到“*设置名称服务器*”:

[![](img/24142cea047463a5024351bf84f29a16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B7BEXI38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoMODYbyvgatOCf7LPLh9MA.png)

单击以编辑名称服务器，并将其设置为:

*   ns1.digitalocean.com
*   ns2.digitalocean.com
*   ns3.digitalocean.com

一定要保存。

**3。数字海洋上的设置域:**

此时，您将转到您的 DO 仪表盘，并转到“网络”部分:

[![](img/6f7cd7c7338f58cb53f1497d44a1ce71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zq_hy5W4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AweS1LwyERVgyW9Gdt_P-QQ.png)

输入您的新域名，点击“*添加域名*”。

接下来，您将输入您所在领域的相关记录。

目前最重要的是 **A 记录。**这些类型的记录允许我们将我们的服务器的 IP 指向我们的域，因为 DO 负责我们的 DNS 域名服务，我们的服务器将被返回给用户。

在**主机名**部分，输入“ **@”。**这意味着在您的域名之前输入的任何内容，例如:

```
http://myDomain.com 
```

…将被定向到您的站点。

在**将指向**部分，从列表中选择您的 droplet。您可以保留 [**TTL**](https://en.wikipedia.org/wiki/Time_to_live) 的默认值，点击“创建记录”。

[![](img/cf29b6d55bc94fa055b8b0b75fb90c37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ub4jBjlM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1019/1%2ANPCQEIl7chjpvNo8r5xAzg.png)

为了给在您的域之前输入的任何**子域**设置一个*通配符*，例如:

*   blue.mydomain.com
*   red.mydomain.com
*   你明白了…

…您需要设置一个 **CNAME** 记录，如下所示:

[![](img/3cbbe0d4d781b1af31ee1bab7faa820b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2B6SrDJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1007/1%2AkuxXeaDL5V3UvbgEgixL_g.png)

这就是你现在所需要的——如果你确实需要添加邮件 DNS 记录，例如 Gmail——你可以选择**MX**, digital ocean 实际上有一个自动导入器，你可以使用它来设置这些记录:

[![](img/4a1ee116c3e14f720efff250e24cf2a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqYetpO8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWxdrejVjxQhyQiVgrgfcRA.png)

一旦你做了，他们的记录可能需要 24 小时才能生效，但实际上我总是只需要等几分钟。

您可以通过在本地机器上启动我们的终端，并[ping](https://en.wikipedia.org/wiki/Ping_(networking_utility))我们的域来测试您是否做了所有正确的事情:

```
ping mydomain.com 
```

或者一个子域:

```
ping blue.mydomain.com 
```

…您应该得到这样的回报:

[![](img/377c031e7ab626464f9e59cbe38667a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ohBdbxRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/645/1%2AuxRLkqehsc-WshJKX82jow.png)

…更不用说，您还应该能够在您的浏览器中输入域名，这真是太棒了！

*接下来，让我们弄清楚如何将文件从我们的本地机器传输到我们的服务器上，这样你就可以展示我们网站的辉煌了！*

### 如何将网站部署到 web 服务器。

[![](img/932d9a78f252741cf5e3cfe68627cc2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KlcV9-bp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AJn2_sVoW9jWWH7QSbAnBdg.jpeg)

这是“[如何建立自己的基于云的 web 服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)”的第 5 部分

1.  [*创建服务器。*T3】](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)

*2。* [*设置服务器。*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)

*3。* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

*4。* [*设置一个域名(例如 www.myDomain.com)。*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3)

*5。获取服务器上的文件* ***←你来了！***

哇，你现在在最后冲刺阶段。至此你已经具备了能够 c [*创建服务器*](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a) *，s* [*etup 服务器*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866) *，I*[*install 灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *和 s* [*etup 域名*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3) *的知识。*

最后一个难题是你如何把它们放到我们的网络服务器上。您可以在我们的命令行中使用 SFTP 实用程序，但是有一种更简单、更适合初学者的方法。

你将使用 [FileZilla](https://filezilla-project.org/) ，这是一个基本上使用 [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) 协议的工具，通过互联网将文件从你的本地机器安全地发送到你的服务器。

1.  **下载并安装 FileZilla:**

可以从[这里](https://filezilla-project.org/)下载。您应该下载客户端软件包:

[![](img/61445794eb773c81ac85a2c25c5f76ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iLKO9g27--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/286/1%2AvayKdMeKsmVqlnvC59Ot5Q.png)

安装完成后，打开 FileZilla 并按照以下步骤连接您的服务器:

**2。连接到您的服务器:**

*   文件>站点管理器(CMD + S)。
*   从左下方的选项中单击新建站点。
*   使用服务器的身份验证详细信息填写“常规”选项卡中的所有部分:

[![](img/24515df6f8f74a8d243cef7dc4117d89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CaPvlZIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/397/1%2Ao08vGGHzDGSRe0WCCg_U9Q.png)

**注意:**确保您为*协议*选择了 SFTP。

*   单击 connect，您将在 FileZilla 的右窗格中看到您的站点加载。左窗格显示本地计算机上的工作本地目录。

[![](img/ab8b4684eea54b39a153d9e57ce7c813.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLf8yMaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2Ar0FZRlb8UGNXAFU4TT_MaQ.png)

*   在*远程站点*文本区域，您将需要导航到

```
/var/www 
```

*   在这里你会找到 *HTML* 目录/文件夹

**3。上传网站文件:**

*   现在是时候把这些漂亮的充满代码的文件上传到你的 web 服务器上了。
*   如果你还没有一个工作网站，你可以去[这里](https://github.com/davedodea/startbootstrap-creative):

[davedodea/start bootstrap-creative](https://github.com/davedodea/startbootstrap-creative)

…到我的 GitHub 页面，我在那里留下了一个示例网站供您使用。

*   如果您知道如何使用 Git 和 GitHub，那么请将存储库下拉到您本地机器上的一个目录中。
*   很可能你没有太多的 Git 经验，这也很酷，从上面的链接中，找到绿色按钮，上面写着“*克隆/下载*”，然后选择“*下载 ZIP* ”。

[![](img/f2e2440feae0769fcedd23f0ac27a9b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--StL-RDEl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjY8HD3swNHS2L8JM-uJStA.png)

*   解压缩下载的文件夹。
*   然后，再次在 FileZilla 中，在左侧窗格中，导航到该文件夹，如下所示:

[![](img/d92018a23c0cc81de2ed581a34348e57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wHmWK8ay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACsZq93coQH7ABUh39WLuKA.png)

*   您应该在左边看到您的本地文件，在右边看到远程服务器。
*   现在只需突出显示左侧的文件/文件夹，并将它们拖到右侧(或右键单击并选择上传)。根据上传的大小，可能需要一两分钟的时间。

### 一旦上传完成，就大功告成了！现在，您已经有了一个功能齐全的网站，它被上传到了您自己托管在云中的 web 服务器上。

干得好！

如果你浏览到[www.yourdomain.com](http://www.yourdomain.com)，你会看到这个(或者你上传的任何一个网站):

[![](img/b249fd16010ae156d94b0c234efb7c39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Qhh7HI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AigvlABzQIOH6J_vu8ltl-Q.png) 

<figcaption>[功劳。](https://startbootstrap.com/template-overviews/creative/)</figcaption>

因此，概括一下，这些是您在本系列“[如何建立您自己的基于云的 web 服务器](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)”中经历的步骤:

1.  [*创建服务器。*T3】](https://medium.com/@davedodea/how-to-set-up-your-own-cloud-based-web-server-2cb091ec48a)

*2。* [*设置服务器。*](https://medium.com/@davedodea/setting-up-a-new-ubuntu-server-for-the-first-time-e8c301ebf866)

*3。* [*安装灯栈*](https://medium.com/@davedodea/how-to-install-the-lamp-stack-on-a-new-server-128f04575a3b) *。*

*4。* [*设置一个域名(例如 www.myDomain.com)。*](https://medium.com/@davedodea/setup-a-domain-name-for-a-new-website-or-server-b0c1f76397a3)

*5。获取服务器上的文件* ***←你来了！***

我真的希望您喜欢这个系列，并且我期待着关于我在整个过程中提到的主题的未来帖子，例如安全密钥认证、进一步的增强和服务器监控。

如果你有任何问题或补充，请在下面的评论中告诉我，或者在 [Twitter](https://twitter.com/DaveDODea) 上联系我。

~戴夫。

戴夫·奥迪亚(@戴夫·多迪)|推特*