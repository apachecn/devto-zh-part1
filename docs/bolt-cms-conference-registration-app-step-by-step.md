# 使用 Bolt CMS 逐步构建会议注册应用程序

> 原文：<https://dev.to/martin_betz/bolt-cms-conference-registration-app-step-by-step>

这是一个关于如何使用 [Bolt CMS](https://bolt.cm/) 和 [Members](https://market.bolt.cm/view/bolt/members) 扩展创建一个会议注册应用的分步指南。我将与迭代一起工作，从简单到更高级。我将把教程分成几个部分。

第 1 部分:我们应用程序的目标是什么？用户会期待一个会议注册 app 有哪些功能？你如何设置 Bolt CMS，它是帮助我们更快构建应用程序的框架？

## 用户故事

假设你负责组织一个关于未来的大型会议。你租了地方，找了赞助商，预定了一些主题演讲人。此外，你还向你的*未来*邮件列表中的数千名读者发出了邀请。你期望他们中的几百人会来参加你的会议。但是你将如何管理注册呢？如果你的服务员(或者我在这里称他们为会员)只是通过电子邮件回复，你就把提交的内容保存在一个大的 Excel 文件中。嗯，这是一个很好的起点。但是，如果注册的人想要更改细节，或者如果宴会承办人需要实时了解所有食物偏好，该怎么办呢？当然，将 Excel 表放在共享的 Dropbox 文件夹中就足够了。但是:如果你曾经和几个人一起工作在一个共享的 Excel 表格上，你知道它会很快变得混乱。

因此，管理会议注册的应用程序可能会有所帮助。这些是要求:

1.  会员可以用`email`和`password`现场注册
2.  会员可以在注册时或注册后用不同的表格填写个人信息(`firstname`、`lastname`、`foodPreference`、`roomSize`)
3.  提交后，成员可以更改他们的个人信息
4.  登录的成员可以访问带有票证的页面(出于演示目的，这是一个带有通用票证图像的静态站点)
5.  会员应通过电子邮件获得有关注册和详细信息变更的通知
6.  `*`管理员可以将用户数据导出到 Excel
7.  注册参加第二次会议的会员可以重新使用他们的旧数据

最后两项(`*`)是必须具备的，但不是应用程序运行的必要条件。如果这超出了 Bolt 及其扩展的能力范围，我可能只描述简单快捷的解决方法。

## 设置

你需要一个本地开发环境，使用命令行并运行 *PHP 5.6/7* 、 *Composer* 。

我使用 Mac 进行开发，并且使用非常方便的开发工具 [Laravel Valet](https://laravel.com/docs/5.3/valet) 。请使用你和你的机器最适合的，无论是 *MAMP* 、*桌面服务器*还是*对接机*。如果你不在 Mac 上，没有偏好，我可以向你强烈推荐 [Laravel Homestead](https://laravel.com/docs/5.3/homestead) 。

如果终端中的`php -v`显示出`PHP 7.1.0`或任何高于 5.6 的版本号，`composer`会向你显示一个命令列表。

我们可以开始了吗？

(注意:我总是为我的编辑添加行号。这些来自于普通的设置，所以如果你在文件中添加了额外的行，这些数字就不会正确。但即使在这些情况下，他们也会指引你到大概的位置。)

## 安装螺栓

我们将使用 *Composer* 来安装 CMS 本身。`composer create-project bolt/composer-install:^3.2 conference --prefer-dist`

`conference`是应用程序的名称——您可以随意命名，只需记下这个名称，以后可能会用到。请耐心等待，安装程序会下载相当多的库(大约 100 MB)。安装人员会问你一些关于位置的问题，我会用`enter`来确认。当然，你可以在这里根据自己的喜好修改细节。

Bolt 现在准备好进行设置了。你只需要告诉你的本地网络服务器服务于`public`文件夹。最简单的方法是在`public`文件夹中用`php -S localhost:8888`启动 PHP 的内部开发服务器。我更喜欢使用*代客*，它在引擎盖下使用 *Nginx* 。该命令在`public`文件夹中时为`valet link conference`。您现在可以在浏览器中打开`conference.dev`并输入管理员用户。

表单下方有一条注释，通知您正在使用 *SQLite* 。SQLite 是“文件中的数据库”，这使得它比 MySQL 数据库更具移动性。对于编辑人数少(=编辑数据库的人)和访问者人数多(=从数据库和缓存中请求数据)的网站来说，它已经可以生产了。我们的会议应用程序可能会成为许多用户通过他们的条目访问和修改数据库的例子，所以对于一个生产网站，我可能会选择一个成熟的 MySQL 数据库。对于开发来说，SQLite 很好，如果你愿意，你可以稍后从 SQLite[迁移到 MySQL 或 MariaDB。](https://www.symfony.fi/entry/migrating-bolt-cms-from-sqlite-to-mysql-mariadb)

## 设置螺栓和构件扩展

你已经创建了你的管理员用户了吗？我给了他用户名`ticketmaster-262s`(名字没那么好取，很好防止暴力登陆企图)。点击保存，你将被重定向到`conference.dev/bolt`并看到后端。

让我们马上安装[成员](https://market.bolt.cm/view/bolt/members)扩展。我们需要高级用户管理的扩展。对于 Bolt 的普通实例，我们可以添加用户，但是这些用户是后端用户，他们可以访问所有访问者的所有数据。不太好。这就是我们需要延期的原因。但是怎么安装呢？

在左侧边栏中找到`Extend`。在搜索表单中，键入`Members`。当你输入时，会出现一个下拉菜单，你可以在那里选择`Members (Gawain Lynch - bolt/members)`。打`Browse Versions`。哎呀，没有稳定版本。从 Github 回购我知道`dev-master`是最近的一个。所以还是用`Install This Version`装吧。下一次糟糕的是:一个*编写器错误*，大意是“没有针对你的最小稳定性(稳定)的包”。让我们解决这个问题。

在会议的根文件夹中打开文本编辑器并编辑`app/config/config.yml`。从第`399`行开始，像这样修改这些行:

```
extensions:
#    site: 'https://extensions.bolt.cm/'
#    enabled: true
    composer:
        minimum-stability: stable 
```

Enter fullscreen mode Exit fullscreen mode

注意，这是一个 YAML 文件，缩进很重要。它应该是四个字符，没有制表符。如果你有不一致的缩进，YAML 就失败了，所以如果你在这一步有问题，检查你的空格。

保存配置文件并返回到[conference.dev/bolt/extend](http://conference.dev/bolt/extend)。有一个很大的警告标志。按照说明，检查数据库，并通过单击来更新它。

在您的浏览器中打开一个新标签，然后前往[conference.dev/membership/profile/register](http://conference.dev/membership/profile/register)。现在有一个表格，你的网站访问者可以注册参加你的会议并成为会员。

一个有抱负的会员必须在这个普通版本的应用程序中填写的细节是`Public Name`、`Email address`和`Password`。我们需要电子邮件和密码(=用户故事#1)，不需要公开姓名(尽管一个著名科学家的 *CaptainFuture* 姓名徽章会很棒)，并且错过了`firstname`、`lastname`、`foodPreferences`和`roomSize`的字段。然而，对于第一次迭代来说，`public name`、`email`和`password`是可以的。

让我们创建第一个成员:`Captain Future` ( `captain@martinbetz.eu`，`thefuture`)。我将在以后的部分中讨论电子邮件设置，但现在我们只需要一个有效的 URL (conference.dev 将不起作用)。点击`Save`，你应该会看到一个类似这样的调试信息:

```
Registered account captain@martinbetz.eu ()
Editing profile for account captain@martinbetz.eu (a6d3c4ce-c9ed-436a-9faa-0e4e4e57d1c2) 
```

Enter fullscreen mode Exit fullscreen mode

你不仅创建了你的第一个成员，而且你还被锁定为*未来队长*并且可以从前端更改你的详细信息。尝试打开一个新标签，打开[你的个人资料页面](http://conference.dev/membership/profile/edit)。将您的公开姓名改为*未来医生*，点击保存并在另一个选项卡上打开个人资料页面。你还是*未来博士*。

您不能登录和注销；自定义字段丢失，我们还没有看到你的管理员可以看到所有注册的成员。

但是:我们有了我们应用程序的第一个工作版本。那很容易，不是吗？

请继续关注第 2 部分:创建定制字段和定制模板，设置电子邮件并从 Bolt 后端管理您的成员。

(封面图片:[https://farm 9 . static Flickr . com/8631/16087236662 _ 567 a61b 9 AE _ o . jpg](https://farm9.staticflickr.com/8631/16087236662_567a61b9ae_o.jpg))