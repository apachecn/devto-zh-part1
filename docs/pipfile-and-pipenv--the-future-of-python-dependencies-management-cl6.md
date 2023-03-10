# Pipfile 和 Pipenv:Python 依赖关系管理的未来

> 原文：<https://dev.to/djiit/pipfile-and-pipenv--the-future-of-python-dependencies-management-cl6>

是的，我听到了。pip 是一个很好的工具，已经存在很长时间了。但是大约 3 年来，人们(贡献者)一直在寻找一种方法来增强我们的包管理体验。想想你最喜欢的工具 composer，npm(或者更好，yarn)的超能力。

他们提供的(或多或少)是古老的`requirements.txt`文件的替代品:即 **Pipfile** 。

## 我是如何学会不再担心并再次爱上皮普的

新的 [Pipfile](https://github.com/pypa/pipfile) 在几个方面不同于旧式的需求文件:

*   它使用 *TOML* 语法来允许更多的配置；
*   它声明依赖组(即一个`default`加一个`development`,用于...开发包),所以您不必将您的需求分成几个文件。
*   它附带了一个`Pipfile.lock`文件，这个文件固定了你的包的版本，此外还有一些安全奖励。

让我们来看看一个最小的、简单的 Pipfile:

```
[[source]]
url = 'https://pypi.python.org/simple'
verify_ssl = true
name = 'pypi'

[requires]
python_version = '3.6'

[packages]
flask = '*'

[dev-packages]
pytest = '*' 
```

Enter fullscreen mode Exit fullscreen mode

我们这里有什么:

*   首先，我们声明 pip 将要使用的源代码。通常，它会是 *pypi* 。
*   然后我们声明我们的 Python 版本需求。
*   最后，我们声明我们的包和开发包。

明白了吗？下面是来自 GitHub 官方资源库的 Pipfile 示例:

```
[[source]]
url = 'https://pypi.python.org/simple'
verify_ssl = true
name = 'pypi'

[requires]
python_version = '2.7'

[packages]
requests = { extras = ['socks'] }
records = '>0.5.0'
django = { git = 'https://github.com/django/django.git', ref = '1.11.4', editable = true }
"e682b37" = {file = "https://github.com/divio/django-cms/archive/release/3.4.x.zip"}
"e1839a8" = {path = ".", editable = true}
pywinusb = { version = "*", os_name = "=='nt'", index="pypi"}

[dev-packages]
nose = '*'
unittest2 = {version = ">=1.0,<3.0", markers="python_version < '2.7.9' or (python_version >= '3.0' and python_version < '3.4')"} 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？使用 TOML 语法，您可以非常具体地指定想要使用的版本、源代码或操作系统变体。注意，不是所有的包版本都是固定的，即使*是* [声明依赖关系的首选方式](http://nvie.com/posts/pin-your-packages/)。

Pip 将在未来用类似于`pip install -p Pipfile`的语法来支持这个规范。让我们看看今天如何使用这些神奇的东西。

## 回到未来

认识一下 [Pipenv](https://docs.pipenv.org/) (没错，这是肯尼斯·雷茨的另一个项目，他是 [httpbin](http://httpbin.org/) 、[pep8.org](//pep8.org)，以及，是的，[请求](http://docs.python-requests.org/en/master/#)的幕后人)。

> ![Julien Tanay profile image](img/8c77aae9d174fa34e96328cc29b11ed1.png)朱利安·塔奈[@ DJ IIT](https://dev.to/djiit)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)当你他妈的意识到 [@kennethreitz](https://twitter.com/kennethreitz) 的 pipenv 刚刚掉了一个🎃在你的壳里。让我开心了一天。2017 年 10 月 31 日下午 13:14[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=925350303863656448)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=925350303863656448)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=925350303863656448)

Pipenv 是当前 Pipfile 的首选实现，它允许您像老板一样管理您的 virtualenvs。它旨在简化和增强我们管理 python 环境的方式(类似于 *virtualenvwrapper* ，但带有 *pip* 技能)。

下面是一个简单的工作流程:

1-在你的项目目录中，创建你的 *virtualenv* 。Pipenv 会把它存放在一个集中的地方，而不是你的项目根目录:

```
pipenv --three # Yeah, 3 is the way to go 
```

Enter fullscreen mode Exit fullscreen mode

2-安装一些依赖项:

```
pipenv install flask
pipenv install --dev pytest # Notice the --dev flag 
```

Enter fullscreen mode Exit fullscreen mode

3-发挥你的*才能*。它将打开一个新的外壳:

```
pipenv shell 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在执行该命令之前，我们并没有在中使用*。一切都由 *Pipenv* 为我们处理。*

从这一点来看，你可以走了。Pipenv 将帮助您在整个开发过程中管理您的环境。

最后，您不应该担心与您的部署服务器的兼容性问题(当您阅读本文时，它肯定仍在使用 *pip* ):您可以通过运行:
生成一个旧的`requirements.txt`

```
pipenv lock -r > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

Pipenv 处理您的依赖关系，它甚至通过指定每个包的散列来增加一些安全性。

就是这样。去测试 Pipenv，告诉我你的想法！

干杯

*注:本帖最初发布于[媒体](https://medium.com/@djiit/pipfile-and-pipenv-the-future-of-python-dependencies-management-8c0c5b6ec99b)。*