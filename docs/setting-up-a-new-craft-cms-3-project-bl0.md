# 建立一个新的 Craft CMS 3 项目

> 原文：<https://dev.to/gaijinity/setting-up-a-new-craft-cms-3-project-bl0>

# 建立一个新的工艺 CMS 3 项目

### 随着 Craft CMS 3 的发布，你可能想踢踢轮胎。这里是如何建立一个工艺 CMS 3 项目

安德鲁·韦尔奇

[![Craft Cms 3 Rc 1](img/3729ad8cc34642ea7ff1ee15118b1661.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYVOG6ao--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/1732/craft-cms-3-rc-1.jpg)

现在 [Craft CMS 3 已经发布了](http://craftcms.com/news/craft-3)，你似乎很想试一试。您可能想先阅读这篇文章，它会为您提供一个建立项目的路线图。

这篇文章将讨论建立一个新的*工艺 3 项目，这样你就可以用它来进行实验了。它将*而不是*覆盖现有的 Craft 2.x 项目到 Craft 3；期待在未来的文章中看到这一点。*

[![Release Candidate Features Frozen](img/d69922e1ae8fcf8e3a5fe61b1a9f5aec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DYe6ZuEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/release-candidate-features-frozen.jpg)

这使得*excel 有时间开始熟悉它，不管你是一个前端开发人员，还是一个优秀的开发人员，或者两者兼而有之。当特性被冻结时，你是在对一个已知的量工作。*

我不会老调重弹什么是新的工艺 3；如果你对此感兴趣，可以看看这篇文章。

标准参考将始终是针对[服务器需求](https://github.com/craftcms/docs/blob/master/en/requirements.md)、[安装](https://github.com/craftcms/docs/blob/master/en/installation.md)、[配置](https://github.com/craftcms/docs/blob/master/en/configuration.md)和[结构](https://github.com/craftcms/docs/blob/master/en/directory-structure.md)的工艺 CMS 文档。但是我也想给这个主题添加一些小技巧、窍门和提示。

所以，让我们开始吧！

## 制作中应该用 Craft 3 吗？

**N.B.** 由于 [Craft CMS 3 于 2018 年 4 月 4 日<sup>日</sup>发售](http://craftcms.com/news/craft-3)，答案是:**def I nity yes**，你应该是在生产中使用 Craft CMS 3.x。这一节的平衡因其历史原因而保留。

《RC1 争霸 3》发布时最常见的问题之一是:

<aside>Should I Use Craft 3 in Production?</aside>

答案是*视情况而定*。我个人正在为一个客户网站以及我自己的一个新项目使用 Craft 3 RC1 制作。

最大的缺点是你最喜欢的插件可能还没有被移植，但是 Craft 足够灵活，如果有必要的话，你通常可以去掉很多插件。也有相当多的[工艺 3 增益](https://plugins.craftcms.com/)已经可用。

我认为这是有意义的，至少要建一个试验场来测试一下 RC1 3 号飞船的轮胎。对于一些客户网站，我认为现在也很容易开始使用它。速度和基础设施的改善是值得的。

## 源自 Craft 2.x 的变化

与 2.x 版本相比，3 版本在设置方面有一些变化，在我看来都是好的方面。

[![Changes Craft 2 To Craft 3](img/48d5cbd2b35e026bf1cd1aa29a26ed0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eMv6VUxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/changes-craft-2-to-craft-3.jpg)

有些变化需要一些时间来适应:

*   飞船 3 现在使用[通讯器](https://getcomposer.org/)来安装&更新飞船和你可能有的任何插件
*   默认目录结构有一些变化；但这些只是建议。你可以用尤尔喜欢任何东西
*   为工艺 2.x *添加的插件 writ ten 将无法在工艺 3 中使用*,它们需要由开发者更新
*   Craft 3 现在默认使用一个 [php dotenv](https://github.com/vlucas/phpdotenv) `.env`文件来处理 envi ron men tal-spe cif ic 之类的东西，比如密码、数据库名称等等。
*   你可以对模板做一些小的改动；大多数只是版本，所以它们仍然有效，但是你需要在 Craft 4 发布的时候更新它们

## 用 pos er

第一个主要变化是 Craft 3 依赖于 [Com pos er](https://getcomposer.org/doc/) 来安装和更新 Craft CMS 本身以及你可能使用的任何插件。

*   [Com pos er](https://getcomposer.org/doc/) 是一个允许安装 PHP 包的工具，很像 JavaScript 的`npm`和`yarn`
*   Pack ag ist .org 是一个网站，它公布了所有的计算机软件包，类似于 JavaScript 的 [npmjs .com](https://www.npmjs.com/)

如果你还没有在你的`local`开发设置上安装计算机，这里有关于如何安装计算机的[指导。](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos)

Pix el & Ton ic 表示，他们计划允许一个非 Com pos er 安装路径，但随着开发人员的开发，它实际上使我们的生活更容易接受 Composer。

因此，如果你不熟悉计算机，现在就花点时间学习它，将来会有所收获。让我们开始吧！

[![Craft 3 Cms Composer](img/05b48a5f17a511a32beacbbb2e42ab74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8paBfm5G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-3-cms-composer.jpg)

要安装一个新的 Craft 3 项目，我们需要做的就是下面这些:

```
 composer create-project craftcms/craft PATH 
```

让我们来分解一下:

*   `composer`只是[全局安装的 Com 组件](https://github.com/craftcms/docs/blob/master/en/installation.md#1-install-composer)命令
*   `create-project`告诉 Com pos 我们想要[从一个现有的模板中创建一个新项目](https://getcomposer.org/doc/03-cli.md#create-project)(稍后将详细介绍)
*   在创建我们的项目时， [pack ag ist .org](https://packagist.org/) pack age 是我们想要用作模板的吗
*   是我们希望我们的项目在何处创建的相对或绝对路径；通常不会是像`craft3`或`~/webdev/sites/mysitename`之类的东西

因此，在我用于本地开发的[主虚拟机](https://dev.to/gaijinity/local-development-with-vagrant-homestead-5gb0-temp-slug-2094211)中，我将运行的命令是:

```
 vagrant@homestead ~/sites $ composer create-project craftcms/craft crafttest 
```

一大堆文字将会飞逝而过，但让我们来谈谈到底发生了什么。Com pos er 要去 [pack ag ist .org](https://packagist.org/) 寻找一个名为`craftcms/craft`的 pack age，然后基本上是`git clone`回复，但实际上没有下载`.git`的数据。

毕竟，我们正在创建一个新的项目，所以我们想把这个包作为一个模板，把其中的文件作为一个起点。

看看 GitHub .com 上的 actu al[craft CMS/craft repos I to ry，你会发现其实没什么大不了的。只是一些工艺配置文件、`composer.json`等。所以这只是一个基本的项目，作为我们的起点。](https://github.com/craftcms/craft)

然后它会运行一个`composer install`来安装任何一个`craftcms/craft`声称依赖的组件包，就像`npm install`或`yarn`对 npm 包所做的那样。

在这里我要理解的关键是,`craftcms/craft` pack age 只是用他们认为是你的新的 Craft 3 项目的一个很好的起点建立的像素&吨 ic。这实际上可能是属于`"type": "project"`的任何包 ag ist .org 包页！

例如，我已经设置了一个包龄`nystudio107/craft`，它允许我用我想要的直接结构和依赖度来设置工艺 3，并且我可以用*我的*包开始一个新的工艺 3 项目，通过做:

```
 vagrant@homestead ~/sites $ composer create-project nystudio107/craft crafttest 
```

这个项目包做了 Pix El&Ton IC[craft CMS/craft](https://github.com/craftcms/craft)项目包做的每一件事，然后安装了一些我使用的工具和 scaf fold ing，在这篇博客的文章中已经有所描述。所以这是一个很好的起点。

这不仅对你想建立自己的 scaf 文件夹有用，而且对理解正在发生的事情也很重要。让我们来看看`craftcms/craft`包附带的`composer.json`文件:

```
 {
  "name": "craftcms/craft",
  "description": "Craft CMS",
  "keywords": [
    "craft",
    "cms",
    "craftcms",
    "project"
  ],
  "license": "MIT",
  "homepage": "https://craftcms.com/",
  "type": "project",
  "support": {
    "email": "support@craftcms.com",
    "issues": "https://github.com/craftcms/cms/issues",
    "forum": "https://craftcms.stackexchange.com/",
    "source": "https://github.com/craftcms/cms",
    "docs": "https://craftcms.com/docs",
    "rss": "https://craftcms.com/changelog.rss"
  },
  "require": {
    "craftcms/cms": "^3.0.0",
    "vlucas/phpdotenv": "^2.4.0"
  },
  "autoload": {
    "psr-4": {
      "modules\\": "modules/"
    }
  },
  "config": {
    "optimize-autoloader": true,
    "platform": {
      "php": "7.0"
    }
  },
  "scripts": {
    "post-create-project-cmd": [
      "@php -r \"copy('.env.example', '.env');\"",
      "@php -r \"unlink('composer.json');\"",
      "@php -r \"unlink('LICENSE.md');\"",
      "@php -r \"unlink('README.md');\"",
      "@php -r \"rename('composer.json.default', 'composer.json');\"",
      "@composer dump-autoload -o",
      "@php craft setup/welcome"
    ]
  }
} 
```

所以这很有趣！`craftcms/craft`项目包将`craftcms/cms`列为依赖项。这可能会有点复杂，所以请记住:

*   你的手工艺 3 项目的起始项目是 scaf 折叠包装吗
*   是包含所有代码的实际工艺 3 包版本，这些代码集体构成了工艺 CMS

所以工艺本身只是另一个被拉进来的复杂包装。它对你可能想要使用的任何插件都是一样的，你可以把它们添加到你的`composer.json`中，它们将作为 PHP 包被安装。

当然，Craft 也提供了一个很好的 GUI 来完成所有这些工作，但是它对于理解事情是如何工作的很有帮助。

如果您想深入了解 Com per，我建议查看文章[Com per stati Flags](https://igor.io/2013/02/07/composer-stability-flags.html)&[Com per Autoload features](http://alanstorm.com/composer_autoloader_part_1/)(本文有几个部分，请务必全部阅读！).

对于理解作曲者/包装者的名字非常有用的还有[包装标签。](https://semver.mwl.be/#?package=craftcms%2Fcms&version=%5E3.0.0-RC1&minimum-stability=RC)

## 工艺 3 初始设置

好的，在我们创建了一个工艺 3 项目后，现在我们需要做一点工作来设置它。

[![Craft 3 Cms Setup Instructions](img/624159b1d5b7a610ac515a589579537c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Eo9jMjrb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x702_crop_center-center_100_line/craft-3-cms-setup-instructions.jpg)

在装置的最后，你会看到一条来自 Craft 的消息:

```
 Generating a security key...
done (UDRBoBjQAzfgY5J9S-543Ie0PrxwCwZG)

Welcome to Craft CMS! Run the following command if you want to setup Craft from your terminal:

    ~/sites/crafttest/craft setup 
```

它生成的**安全密钥**只是一个随机的字符串(就像密码一样),在每个项目中都是唯一的。对于多环境设置，您将希望在每个环境中使用相同的安全密钥。

Craft 用这个来加密数据，所以它只是在`general.php`中设置的一个唯一的种子。

如果你运行命令`~/sites/crafttest/craft setup`(或者它为你的项目输出的任何东西)，它将为你建立你的`.env`文件:

```
 vagrant@homestead ~/sites $ ~/sites/crafttest/craft setup
Which database driver are you using? [mysql,pgsql,?]: mysql
Database server name or IP address: [localhost]
Database port: [3306]
Database username: [root] homestead
Database password:
Database name: crafttest
Database table prefix:
Testing database credentials... success!
Saving database credentials to your .env file...
done 
```

你的(emp ty)数据库需要已经创建，就像 Craft 2.x 上一样。然后它会问你是否想现在安装 Craft。如果你说**是**，它实际上只是一个家庭设置向导的命令行等价物:

```
 Install Craft now? (yes|no) [yes]:

Username: [admin]
Email: andrew@nystudio107.com
Password:
Confirm:
Site name: crafttest
Site URL: http://crafttest.dev
Site language: [en-US] 
```

当然，你不必这样做。你可以说**不**，如果你喜欢的话，你可以像安装 Craft 2.x 一样，通过进入`[http://mysite.dev/admin](http://mysite.dev/admin)`网址来安装它。

无论哪种方式，您现在都会在您的项目根目录中有一个 [php dotenv](https://github.com/vlucas/phpdotenv) `.env`文件，其中包含您的 credentials:

```
 vagrant@homestead ~/sites $ cat crafttest/.env
# The environment Craft is currently running in ('dev', 'staging', 'production', etc.)
ENVIRONMENT="dev"

# The secure key Craft will use for hashing and encrypting data
SECURITY_KEY="UDRBoBjQAzfgY5J9S-543Ie0PrxwCwZG"

# The database driver that will used ('mysql' or 'pgsql')
DB_DRIVER="mysql"

# The database server name or IP address (usually this is 'localhost' or '127.0.0.1')
DB_SERVER="localhost"

# The database username to connect with
DB_USER="homestead"

# The database password to connect with
DB_PASSWORD="secret"

# The name of the database to select
DB_DATABASE="crafttest"

# The database schema that will be used (PostgreSQL only)
DB_SCHEMA="public"

# The prefix that should be added to generated table names (only necessary if multiple things are sharing the same database)
DB_TABLE_PREFIX=""

# The port to connect to the database with. Will default to 5432 for PostgreSQL and 3306 for MySQL.
DB_PORT="3306" 
```

记住，这个文件从来没有被签入 git，所以我们需要为我们部署 Craft 的每个环境创建一个人工创建的`.env`文件，包括我们的`staging`和`live`生产服务器。

解析`.env`文件中的内容，并设置为`general.php`、`db.php`等读取的 [PHP 环境变量](http://php.net/manual/en/reserved.variables.environment.php)。工艺 3 `config/`文件:

```
 <?php
/**
 * Database Configuration
 *
 * All of your system's database connection settings go in here. You can see a
 * list of the available settings in vendor/craftcms/cms/src/config/DbConfig.php.
 */

return [
    'driver' => getenv('DB_DRIVER'),
    'server' => getenv('DB_SERVER'),
    'user' => getenv('DB_USER'),
    'password' => getenv('DB_PASSWORD'),
    'database' => getenv('DB_DATABASE'),
    'schema' => getenv('DB_SCHEMA'),
    'tablePrefix' => getenv('DB_TABLE_PREFIX'),
    'port' => getenv('DB_PORT')
]; 
```

虽然`.env`文件对于开发来说很棒，但是对于现场制作，我建议将环境变量[移动到您的 web 服务器配置](https://github.com/vlucas/phpdotenv#usage-notes)，或者使用像[Craft 3 multi environment](https://github.com/nystudio107/craft3-multi-environment)这样的东西，如在【Craft CMS 的 multi environment config 文章中所述。

## 飞船 3 号直奔 ry 结构

好了，现在我们已经安装并设置了一个新的 Craft 3 项目，让我们来看看我们实际安装了什么。

[![Craft 3 Cms Directory Structure](img/5a631b8eb76f1579b58f1396c19db35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iLSAyuQe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-3-cms-directory-structure.jpg)

这是我们新安装的工程目录的样子:

```
 vagrant@homestead ~/sites/crafttest $ tree -L 2 -a .
.
├── composer.json
├── composer.lock
├── config
│ ├── app.php
│ ├── db.php
│ ├── general.php
│ ├── redactor
│ └── routes.php
├── craft
├── craft.bat
├── .DS_Store
├── .env
├── .env.example
├── .gitignore
├── LICENSE.md
├── modules
│ └── Module.php
├── README.md
├── storage
│ ├── .gitignore
│ ├── logs
│ └── runtime
├── templates
│ └── .gitkeep
├── vendor
│ ├── autoload.php
│ ├── bin
│ ├── cebe
│ ├── composer
│ ├── craftcms
│ ├── creocoder
│ ├── danielstjules
│ ├── .DS_Store
│ ├── enshrined
│ ├── ezyang
│ ├── guzzlehttp
│ ├── justinrainbow
│ ├── league
│ ├── mikehaertl
│ ├── paragonie
│ ├── pixelandtonic
│ ├── psr
│ ├── seld
│ ├── swiftmailer
│ ├── symfony
│ ├── twig
│ ├── vlucas
│ ├── yiisoft
│ └── zendframework
└── web
    ├── cpresources
    ├── .htaccess
    ├── index.php
    └── web.config

33 directories, 22 files 
```

你会注意到的第一件事是这里不再有`craft/`目录，这是我们过去在 Craft 2.x 中看到的一些东西。Craft 2.x 中`craft/`目录中的每个目录现在都在我们的根项目目录中。

如果你仔细想想，这是有意义的，因为 Craft 本身现在只是作为另一个 Com pos er depen den cy 安装在`vendor/craftcms`目录中。

现在，让我们来看看我们项目中一些值得注意的文件/目录:

*   `composer.json`拥有我们项目的所有信息，包括 PHP 包版本依赖关系及其最小版本[版本](https://semver.npmjs.com/)
*   `composer.lock`有关于 PHP 包年龄的信息&他们的版本*实际安装*(他们可能比我们的最低版本新)
*   `config/`是以前的`craft/config/`，有所有的 famil iar Craft CMS 配置文件
*   `craft`是一个命令行工具，完全类似于 web facing`index.php`。它是你用来通过命令行运行 Craft 3 的工具(下面会详细介绍)
*   是我们的 [php dotenv](https://github.com/vlucas/phpdotenv) `.env`文件，里面有我们的环境-spe cif ic 之类的东西，比如数据库密码等等。存信息的
*   告诉 git 完全忽略哪些文件模式，并且永远不要签入 git
*   `storage/`是以前的`craft/storage/`，是临时运行时文件、日志等的地方。就像在 Craft 2.x 中一样
*   `templates/`是以前的`craft/templates/`，是你存放树枝模板的地方
*   是你的项目使用的所有 PHP 包依赖项(包括 Craft 本身)被存储的地方
*   `web/`是曾经的`public/`，包含我们的 index.php，。htac cess 等。文件，并且是 web 服务器根目录应该指向的位置

重要的是要注意，Craft 并不真正关心你用什么作为直接结构，这只是 Pix el & Ton ic 提供给你的一个基础设置。如果你不喜欢从`public/`到`web/`的变化，那也没关系。改名就好！

`craft`命令行工具实际上就是我们以前用来建立`.env`文件，选择安装工艺的工具。就把它想象成从命令行运行 Craft 的一种方式。它里面有一些来自 [Yii2 Con sole Com mands](http://www.yiiframework.com/doc-2.0/guide-tutorial-console.html) 的工具，Pix el & Ton ic 也给它增加了一些工艺类 cif ic 的东西。

例如，您可以使用`./craft install/plugin my-plugin-handle`从命令行安装插件。酷！

## 部署 ing Craft CMS

计算机如何用于工艺 3 的变化直接导致了我们应该如何更新、测试和部署工艺 3。

对于 Craft 2.x，我们只是将所有的`craft/`(包括`vendor/`子目录到 ry)签入 git，在`local` dev 中更新&测试它，然后通过我们选择的部署工具将其部署到`staging`/`live`product，例如 [Lar avel Forge](https://forge.laravel.com/) 、 [Buddy.works](https://buddy.works/) 、 [Lar avel Envoy er](https://envoyer.io/) 、 [Deploy Bot](https://deploybot.com/) 等。

[![Deploying Craft 3 Cms](img/fd3d29b19b49f85af13e1ba2321e052c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h6T2feF3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/deploying-craft-3-cms.jpg)

有几个重要的方面我们需要先了解一下，看看这在 3 中需要如何改变:

*   有关于我们项目的所有信息，以及它最少需要什么样的 PHP 包依赖关系[永远版本](https://semver.npmjs.com/)
*   `composer.lock`有关于实际安装了什么 PHP 包版本依赖的信息*(我们可能有比我们的最低版本要求更高的新版本)*
**   是一个安装了所有 PHP 软件包的目录(包括 Craft CMS 本身！)*   告诉 git 在跟踪 git 中的变化时要忽略哪些文件模式*

 *这里是 craftcms/ craft 包提供的默认`.gitignore`文件:

```
 /.env
/.idea
/vendor
.DS_Store 
```

这意味着我们永远不会将我们的`.env`文件签入 git(因为将密码这样敏感的东西放入版本控制是一个坏主意)，但是我们也忽略了整个`/vendor`目录。

因此，如果我们没有在`/vendor`目录中检查我们的任何 PHP 包来进入 git(这包括 Craft CMS 本身，记住！)，我们怎么更新部署东西？

<aside>By embrac­ing Com­pos­er, that’s how!</aside>

我们将利用计算机的工作原理:

*   在本地开发中，我们将通过`composer update`更新所有东西(或者我们可以通过`composer update packagevendor/package`只更新 spe cif ic 包年龄)
*   上述步骤也可以通过点击“更新”(对于 Craft CMS 本身或其任何插件)在 AdminCP 用户界面上完成。在引擎盖下，它最终会像上面描述的那样运行
*   这会导致 Com pos 下载并安装更新的包，然后它会将安装的内容写到`composer.lock`文件中
*   然后，我们像以前一样测试`local` dev 中的所有东西，以确保它为黄金时间做好准备
*   因为我们忽略了所有的`/vendor`目录，所以就 git 而言，唯一要改变的是`composer.lock`文件，它列出了实际安装的包的版本

然后在服务器端部署，我们将从我们的项目目录运行下面的代码到服务器:

```
 composer install --no-interaction --prefer-dist --optimize-autoloader 
```

`composer install`所做的是查看`composer.lock`文件中的内容，如果它比本地安装的新，它更新&安装它！你可以在这里看到 [com pos er 安装的全部选项](https://getcomposer.org/doc/03-cli.md#install)。

这样做的好处是，我们的 git repos 中检查到的 cruft 更少，并且我们还可以只单独更新特定的 cif 内容。

也许我们只想更新一个 spe cif ic 插件，我们可以通过从本地 dev 通过`composer update pluginvendor/plugin`只更新那个插件来实现。因为这是写入到`composer.lock`文件中的唯一改变，所以当我们的服务器运行`composer install`时，这是唯一被更新的东西

很好。

唯一的缺点是`composer install`可能比简单的`git pull`执行时间长一点，所以可以通过 [Buddy.works](https://buddy.works/) 、 [Lar avel Envoy er](https://envoyer.io/) 、 [Deploy Bot](https://deploybot.com/) 等查看 atom ic 部署。这可能不是个坏主意。

## 工艺 3 外挂

关于外挂的坏消息是外挂开发者需要为 Craft 3 重新编写外挂。

[![Craft 3 Cms Plugins](img/2947bed9f56f0e8a546998d319cbf853.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cRg5ZpuW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-3-cms-plugins.jpg)

好消息是，Pix el & Ton ic 在开发中对 [Craft 3 Plu g 做出了很好的指导](https://github.com/craftcms/docs#plugin-development)，并且许多 Plu g in 已经被移植。查看[工艺 3 Plu g ins 页面](https://plugins.craftcms.com/)看看已经有什么可用的了。

此外，像[这样的工具可以插入 fac ory。io](https://pluginfactory.io/) 和像[这样的文章，所以你想做一个工艺 3 Plu g in？](https://dev.to/gaijinity/so-you-wanna-make-a-craft-3-plugin-76m-temp-slug-4321262)能为开发者提供帮助。

你能做的最好的事情就是和你的插件供应商核实一下，看看他们把插件移植到 3 号工艺的进度。

Pix el & Ton ic 声明商店中的 Plu g 的测试版本(集成到 Craft 3 AdminCP 中)将与 Craft 3 CMS RC 1 一起发布，但最初只有免费的 plu g ins 可用。开发者现在也可以免费获得付费插件，但是一旦 Craft CMS 3 正式发布，他们将需要购买。

付费 plu g ins 商店、Com merce 2 的全面推出，以及 Craft 3 的全面发售将于 2018 年 4 月 4 日<sup>日</sup>开始。查看[我们在 Dot All 2017](https://craftcms.com/news/dot-all-2017-announcements) 发布的文章了解详情。

说到插件，Craft 3 默认没有富文本字段。你需要先安装中的[红笔(别担心，你现有的内容还在)。这是因为在不久的将来，P & T 也将提供一个](https://github.com/craftcms/redactor)[支持的富文本插件。](https://ckeditor.com/)

## Tem 板变化

好消息是，就你的小枝模板的实际变化而言，没有那么多。Pix el & Ton ic 已经做了很好的工作来链接 [Twig 2](https://github.com/craftcms/docs/blob/master/en/changes-in-craft-3.md#twig-2) 中的变化以及 [Craft Tem plate Tags](https://github.com/craftcms/docs/blob/master/en/changes-in-craft-3.md#template-tags) 中的变化，你需要知道这些变化。

绝大多数的变化仅仅是预测误差。这意味着 Pix el & Ton ic 计划在《星际争霸 4》上映时逐步淘汰它们，但目前它们仍然有效。

你可以通过**实用工具** → **定位错误**来检查工艺 3 内部的任何定位错误。

[![Craft 3 Cms Deprecation Errors](img/71a7e5ed0e96674fe8c98e851aa629ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EcKL4Pfe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x472_crop_center-center_100_line/craft-3-cms-deprecation-errors.png)

另一个真正有趣的事情是，在 Craft 3 中，整个`craft.app`应用程序实例可以用于你的 Twig 模板。

这实际上意味着你可以通过 Twig 用 Craft 3 APIs 做任何你可以通过 PHP 做的事情。如此多的小插件被用来展示一些需要的功能，可能不再有必要了。

[![Phpstorm Craft Auto Completion](img/6ebfb7eb11febf1d8cd23e965f0d1373.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y8bExqxw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_576x305_crop_center-center_100_line/phpstorm-craft-auto-completion.png)

如果你在使用 [Php Storm](https://www.jetbrains.com/phpstorm/) 的话，你能做的一件绝对*令人惊奇的*的事情就是在你的 Twig 模板中获得整个`craft.`API 的完全自动编译！要制作 mag ic hap pen，请在中安装[Sym fony plug g，然后转到**首选项** → **语言&框架工作**→**PHP**→**Sym fony**并检查**启用该项目的 plug in**。](https://plugins.jetbrains.com/plugin/7219-symfony-plugin)

然后你需要做的就是把这个检查提示放在你的 Twig 模板的顶部:

```
 {# @var craft \craft\web\twig\variables\CraftVariable #} 
```

…就这样，你在你的小模板中得到和你在 PHP 代码中相同的自动编译。由于整个 Craft 应用程序现在都可以在我们的 Twig 模板中获得，这使得编写使用 Craft 应用程序 API 的 Twig 代码变得更好。

## 把 ping 包起来！

毫无疑问，这是我将在飞船 3 上写的几篇文章的第一篇。但希望它能给你一个关于工艺发展的概念。

前进，做出酷的东西！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计*