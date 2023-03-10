# 将 Django 应用程序部署到 Heroku

> 原文：<https://dev.to/asciidev/deploying-django-apps-to-heroku>

在花了很长时间试图用 Django 开发完美的 web 应用程序后，感觉时机成熟了，心情也准备好了，可以把它放到现实世界中，让用户享受，让你的银行账户开始微笑——因为很明显，没有用户就没有利润。

但是当我们负担不起任何昂贵的服务器托管网站时会发生什么呢？在本文中，我将向您展示如何免费部署一个基于 Django 的 web 应用程序( [www.hellosam.ng](http://www.hellosam.ng) )到 Heroku。

在我们开始之前，这个叫做 **Heroku** 的东西和**部署**的魔法背后是什么？

Heroku 是一个平台即服务网站，允许您免费托管具有基本功能的 web 应用程序。您可以随时在 www.heroku.com 开设账户。

而部署包括将我们的 web 应用程序推送到远程服务器以便受众可以访问它的过程。

太好了！现在我们可以开始了，首先，Heroku 需要安装在我们的开发机器上。(没装 Heroku？那么我们只是在互相欺骗)

注意:我运行的是 Linux (Ubuntu)机器，虽然这里显示的大部分代码都是针对相同的操作系统用户编写的，但是我也为运行在其他操作系统上的其他用户提供了替代方案。

要在 Ubuntu 上安装 Heroku 终端应用程序，请在您的终端中键入以下命令:

```
 sudo add-apt-repository “deb https://cli-assets.heroku.com/branches/stable/apt ./”
    curl -L https://cli-assets.heroku.com/apt/release.key | sudo apt-key add -
    sudo apt-get update
    sudo apt-get install heroku 
```

在 windows 上，你可以下载 32 位的版本或者 T2 的 64 位版本。

对于 MAC 用户，你可以下载并运行[安装程序](https://cli-assets.heroku.com/branches/stable/heroku-osx.pkg)，或者如果你已经安装了 homebrew，你可以运行下面的命令来安装它:

```
 brew install heroku 
```

安装后，打开您的终端并登录到您的 Heroku 帐户。

```
 heroku login 
```

厉害！现在我们已经成功地在本地机器上安装了 Heroku，现在可以开始准备我们的 Django web 应用程序来部署到 Heroku。

下一步是在我们的 web 应用程序的根目录下创建一个 **requirements.txt** 文件。我们需要这个 **requirements.txt** 文件来让 Heroku 知道我们的应用程序是一个 Python(Django)项目，否则它不会被识别。即使您的 web 应用程序没有依赖项，您也可以简单地创建一个空的 **requirements.txt** 文件。文件 **requirements.txt** 有助于列出应用的依赖项及其版本，这使得 Heroku 知道要部署哪些依赖项以及合适的版本。

要快速生成一个 **requirements.txt** 文件，如果你不知道你的应用程序有什么依赖关系，或者你懒得从头开始创建一个(像我一样)，你需要启动你的虚拟环境，在你的应用程序的根目录下，运行终端:

```
 pip freeze > requirements.txt 
```

上述命令将从命令“pip freeze”中获取您的依赖项列表，并将它们复制到 **requirements.txt** 文件中，如果该文件不存在，它将自动为您生成。

一旦成功生成，我们需要在同一个项目根中生成一个 Procfile。该文件定义了进程类型，并告诉应该执行什么命令来启动应用程序。它看起来会像这样:

```
# Procfile
    web: gunicorn blog.wsgi –log-file - 
```

我的 Procfile 与您的 proc file 之间的唯一区别是“”之前的部分。wsgi。这一部分基本上就是项目的名称，因为 wsgi 文件驻留在项目的文件夹中。

接下来，我们需要创建一个 runtime.txt 文件。这个文件将显式地携带您的项目所使用的 Python 的特定版本。要做到这一点，很抱歉没有捷径可走，你必须在 Django 项目根目录下创建一个“runtime.txt”文件，并放入你正在使用的 Python 的确切版本，格式如下:

```
# Runtime.txt
    python-2.7.12 
```

要获得您正在使用的 Python 的确切版本，请在激活虚拟环境的终端中运行以下命令:

```
 python –version 
```

接下来，我们必须设置我们的静态资产，以便我们的应用程序的前端设计在部署到服务器时不会中断。在我们继续之前，我们需要安装 **dj_database_url** 和**whiten noise**:

```
 pip install dj_database_url
    pip install whitenoise 
```

安装后不要忘记更新我们的 requirements.txt 文件:

```
 pip freeze > requirements.txt 
```

要配置我们的应用程序的静态相关参数，我们需要进入 settings.py 文件，并相应地更改文件的某些部分或进行编辑。

注意:如果你已经设置好了这些设置，并且工作正常，你不需要改变它:

```
# Settings.py
    STATIC_URL = '/static/'

    STATICFILES_DIRS = [
      os.path.join(BASE_DIR, "static"),
    ]
    STATIC_ROOT = os.path.join(BASE_DIR, "static_cdn")
    STATICFILES_STORAGE = 'whitenoise.django.GzipManifestStaticFilesStorage'
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, "media_cdn") 
```

此外，我们需要将 settings.py 中的数据库声明更改为以下内容:

```
# Settings.py
    DATABASES = {'default': dj_database_url.config()} 
```

在 settings.py 文件顶部导入 dj_database_url 时:

```
# Settings.py
    import dj_database_url 
```

注意:我们不需要将安装的 whitenoise 添加到基于项目目录的 wsgi.py 文件中，

在这一步之后，我们的 wsgi.py 文件应该看起来如下所示:

```
# WSGI.py
    import os
    from django.core.wsgi import get_wsgi_application
    from whitenoise.django import DjangoWhiteNoise

    os.environ.setdefault(“DJANGO_SETTING_MODULE”, “blog.settings”)

    application = get_wsgi_application()
    application = DjangoWhiteNoise(application) 
```

我们需要在 settings.py 文件中编辑的最后一件事是 ALLOWED_HOSTS 部分。在这里，我们需要输入域名，用户将使用该域名来访问我们出色的 web 应用程序，否则当我们最终部署时，它会抛出错误。

同样，我们需要关闭调试选项。下面是编辑后两行的样子:

```
# Settings.py
    DEBUG = False
    ALLOWED_HOSTS = [â€˜example.herokuapp.com’] 
```

最后，我们准备部署！呀！，但在我们最终部署之前，最好是我们构建这个应用程序并在本地运行它，以确保我们在部署时没有任何错误。

注意:这一步是可选的，但我总是这样做，因为它可以帮助我提前看到任何错误，这样我就可以完全修复并立即正确部署。

运行此命令在本地安装所有依赖项:

```
 pip install -r requirements.txt 
```

然后运行:

```
 heroku local 
```

您的应用程序现在应该在 [http://0.00.0.0:5000](http://0.00.0.0:5000) 上运行。如果这成功了，这意味着你现在可以毫无问题地成功推进到 Heroku。

注意:您可能会遇到类似“您引用的一个静态文件丢失了”这样的错误，要解决这个问题，您需要做的就是删除对静态文件的引用。

现在，我们可以部署到 Heroku:

```
 heroku login

    git add .

    git commit -m “Added for deploy”

    heroku create â€˜name-of-app-without-the-quotes’ 
```

注意:你可以在命令中省去 app 的名字，Heroku 会自动为你生成一个名字。

最后运行:

```
 git push heroku master 
```

打开你的浏览器，瞧！您的 web 应用程序是实时的，可供用户实时交互！。