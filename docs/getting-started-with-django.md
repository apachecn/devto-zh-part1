# 《Django 入门》

> 原文：<https://dev.to/kolokodess/getting-started-with-django>

不熟悉 Python？或者已经在跳探戈了，你觉得是时候涉足 web 框架魔法了？

**认识 Django** :
DJANGO 是一个完全基于 python 构建的 web 框架，它是免费的、开源的，也遵循模型视图控制器模式，(在这种情况下；模型、模板、视图——其中视图与控制器相关，模板与 MVC 模式中的视图相关。对于新手来说，这一部分可能有点混乱，但是随着时间的推移，您将会更加理解这背后的逻辑:)。django 上已经有很多流行的网站，例如: *instagram、pinterest 等*
[![alt text](img/f221a0e31c25b93744ded55c199dfbaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ab_QV6ye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/892l0g65tnxh2fyfxhpm.png)

> Django 上运行的站点

Django 还在这里提供了一些优秀的文档，以及一些特性和工具，其中包括:

*   一种很好的模板语言。
*   像 CSRF 这样的安全特征
*   用于开发和测试的优秀轻量级服务器

在本教程中，我将向你展示如何建立并运行你的第一个 Django 网站。在我们开始之前，我们需要在我们的系统上下载并安装 python，要下载并安装 Python，请点击[此处](www.python.org)。
注意:你需要对 python 有一个基本的了解，而且我将在基于 linux 的系统上运行本教程，所以大多数命令将遵循 Linux 的方式。但是在大多数情况下几乎没有什么不同。
为了确保完全下载，打开您的终端并输入

```
python 
```

一个交互式外壳出现了:

```
Python 2.7.12 (default, Nov 19 2016, 06:48:10)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

要退出，键入 **ctrl + z** 。

为了有一个更整洁的安排，为你的项目创建一个目录总是明智的

```
mkdir folder_name 
```

然后，使用以下命令将 cd 放入项目:

```
cd folder_name 
```

(在此文件夹中，将执行所有其他步骤)

**设置您的虚拟环境:**

接下来我们需要做的是建立我们的虚拟环境，虚拟环境帮助你在你的机器上运行几个版本的 python/django(例如，你可能有两个不同的 python/django 项目运行在不同的版本上，为了避免它们冲突，给你空间运行它们而不出错，虚拟环境来拯救你。一个虚拟环境=一个 python/django 版本)。强烈建议始终使用虚拟环境。
为了设置我们的虚拟环境，我们将使用 python 的包管理器 pip 来进行安装，键入:

```
pip install virtualenv 
```

安装完成后，是时候创建一个虚拟环境，让我们能够使用我们选择的首选 django 版本:

```
virtualenv env_name 
```

*注意:env_name 应该替换为您的环境的首选名称。(我喜欢用安装在其中的 django 版本来命名我的环境，以便于识别)。*

**激活虚拟环境:**

要为 linux/Mac OS 激活我们的虚拟环境:

```
source env_name/bin/activate 
```

对于 windows:

```
env_name/script
activate 
```

**安装 Django:**
现在是在我们的机器上安装 Django 的时候了:

```
pip install django==1.8 
```

使用==1.8 只是给 django 一个关于您想要安装的特定版本的指示，在本例中是版本 1.8。要继续下载最新版本，输入 pip install django。

**开始一个项目:**
现在我们已经让 django 开始运行了，是时候开始我们的第一个项目了！呀！。仍然在我们的命令行中，键入:

```
django-admin.py startproject project_name 
```

*注* : project_name =你的项目名称。在这种情况下，我们将使用 mask_off 作为项目名称。

这将创建一个名为 mask_off 的子文件夹，其框架结构为

mask _ off
**init**。py□T4□settings . py□t5□□URLs . py□T6□□wsgi . py□T7□manage . py□

Django 通过创建上面的文件给了我们更多的方便:
1)****init**。py** 帮助 python 将目录视为包含包；以便防止具有公共名称的目录无意中隐藏在模块搜索路径上稍后(更深)出现的有效模块。在大多数情况下，它通常是一个空文件。
2)**settings . py**文件包含您的项目所需的所有设置，随着我们的进展，我们会经常访问该文件。
3)**WSGI**(web 服务器网关接口)充当我们的 Web 服务器用来与我们的 Web 应用程序交互的接口。点击阅读更多相关信息[。](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface)

**运行服务器:**
没有比访问自己的网页更有趣的事情了，所以让我们运行我们的服务器，它也会生成一个链接，让我们可以查看我们的网页

```
python manage.py runserver 
```

这显示了…

```
python manage.py runserver 
Performing system checks...
System check identified no issues (0 silenced).
You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.
July 12, 2017 - 15:19:01
Django version 1.8, using settings 'mask_off.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C. 
```

*注意到关于未应用迁移的警告消息了吗？现在让我们来谈谈迁移，这是一个简短但非常重要的话题。*

**进行迁移:**
迁移有助于我们在不丢失任何数据的情况下对数据库模式进行更改，每次我们创建新模型或对当前模型进行更改并运行迁移时，它都有助于用模式更新我们的数据库表，而不必亲自拖动和重新创建数据库。

为了实现我们的迁移:

```
python manage.py migrate 
```

这样的输出应该会出现:

```
Operations to perform:
  Synchronize unmigrated apps: staticfiles, messages
  Apply all migrations: admin, contenttypes, auth, sessions
Synchronizing apps without migrations:
  Creating tables...
    Running deferred SQL...
  Installing custom SQL...
Running migrations:
  Rendering model states... DONE
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying sessions.0001_initial... OK 
```

这意味着成功的迁移，现在我们可以成功地运行我们的服务器，没有任何问题。我们在网页上看到一条成功消息，如下图所示
[![alt text](img/47c1221959259c6b67508fdd93ba532f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mKQkVwZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nx0e7ceymnty9q5q0512.png)

> 呀！我们自己的网页。

现在我们有了自己的服务器和一个“网页”，但 django 很高兴地提醒我们，在我们可以自豪地称之为网页之前，我们还有很多工作要做。

本教程的第二部分将包含:在我们的项目中创建新的应用程序，使用 URL，模板，创建视图和链接页面。现在，花更多的时间一遍又一遍地检查这些步骤，以便更加熟悉它们。只有熟能生巧！

从本教程中学到的东西:

*   设置虚拟环境(下载、激活虚拟环境)
*   安装 Django
*   创建项目
*   基本项目组件
*   迁移
*   运行服务器

在这个过程中遇到了什么问题吗？让我知道！我很乐意帮忙

*原载于我的媒体刊物[此处](https://goo.gl/v7Cw8L)T3】*