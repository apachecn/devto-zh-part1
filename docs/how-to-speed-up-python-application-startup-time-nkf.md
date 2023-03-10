# 如何加快 Python 应用程序的启动时间

> 原文：<https://dev.to/methane/how-to-speed-up-python-application-startup-time-nkf>

我听说 [pipenv](https://pypi.python.org/pypi/pipenv) 9.0.2 发布时对启动时间进行了重大改进。

> ![Kenneth Reitz 🐍 profile image](img/a0f59b812abbebdaa722db773f611639.png)肯尼斯·雷兹🐍[@ kennethreitz](https://dev.to/kennethreitz)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)刚刚发布的 Pipenv 9 . 0 . 2，其中包含了海量的 CLI 加速！[pipenv.org](https://t.co/AGD8Hkq1EG)2018 年 1 月 16 日下午 15:53[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=953294094029262849)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=953294094029262849)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=953294094029262849)

很快就试了一下，没觉得快。所以我用 Python 3.7 的新特性调查了一下。

在本文中，我将介绍这个特性以及如何使用它。

* * *

## 启动时间≒导入时间

例如，`pipenv -h`的执行时间比显示帮助信息的时间要长得多。

一般来说，当应用程序启动时，有一些启动过程，如加载环境变量或配置文件。

对于 Python 应用程序，导入模块花费了大部分启动时间。比如`pipenv --version`用了 800ms 左右，`import pipenv`用了 700ms。

```
$ time local/py37/bin/python3 -c 'import pipenv'

real    0m0.696s
user    0m0.648s
sys     0m0.048s

$ time local/py37/bin/pipenv --version
pipenv, version 9.0.3

real    0m0.812s
user    0m0.761s
sys     0m0.052s 
```

Enter fullscreen mode Exit fullscreen mode

## 显示模块的导入时间

Python 3.7 有一个新特性，可以显示导入模块的时间。

该功能通过`-X importtime`选项或`PYTHONPROFILEIMPORTTIME`环境变量启用。

例如，您可以通过
分析 pipenv 的导入时间

```
python3.7 -X importtime -c 'import pipenv' 2> pipenv-imports 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
PYTHONPROFILEIMPORTTIME=1 pipenv --version 2>pipenv-imports 
```

Enter fullscreen mode Exit fullscreen mode

[以下是`pipenv --version`](https://paste.ubuntu.com/26409167/) 的输出示例

## 调查导入时间

在最后的输出中，您可以看到这些行:

```
import time: self [us] | cumulative | imported package
...
import time:      3246 |     578972 |   pipenv.cli
import time:       507 |     579479 | pipenv 
```

Enter fullscreen mode Exit fullscreen mode

在最后一行，579479 意味着`import pipenv`花了 579 479us。

在导入 pipenv 时，会导入许多其他模块。从上面的例子中，您可以看到`pipenv`导入了`pipenv.cli`。子导入缩进两个空格。

再看最后一行。507 表示运行 pipenv 模块时只占用了 507us。579 479 - 507 = 578 972us 用于子导入。

## 寻找慢的部分

让我们从输出中找到慢子树。我选择了一些线条。

```
import time: self [us] | cumulative | imported package
...
import time:     86500 |     179327 | pkg_resources
...
import time:       385 |     236655 |             IPython
import time:        22 |     236677 |           IPython.core
import time:        26 |     236703 |         IPython.core.magic
import time:       978 |     237680 |       dotenv.ipython
import time:       199 |     239032 |     dotenv
...
...
import time:      3246 |     578972 |   pipenv.cli
import time:       507 |     579479 | pipenv 
```

Enter fullscreen mode Exit fullscreen mode

### pkg_resources

如你所见，导入`pkg_resources`很慢。
而令人惊讶的是，`pkg_resources`并不缩进；这不是`pipenv`的子进口。

表示`pkg_resources`是由`pipenv`脚本导入的，不是模块。

```
$ cat local/py37/bin/pipenv
#!/home/inada-n/local/py37/bin/python3.7
# EASY-INSTALL-ENTRY-SCRIPT: 'pipenv==9.0.3','console_scripts','pipenv'
__requires__ = 'pipenv==9.0.3'
import re
import sys
from pkg_resources import load_entry_point

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(
        load_entry_point('pipenv==9.0.3', 'console_scripts', 'pipenv')()
    ) 
```

Enter fullscreen mode Exit fullscreen mode

坏消息:导入`pkg_resources`很慢。这是一个已知的问题，如果不破坏向后兼容性，很难修复。

好消息:可以避免导入 pkg_resources！

```
$ local/py37/bin/pip install wheel
$ local/py37/bin/pip install -U --force-reinstall pipenv
$ time local/py37/bin/pipenv --version
pipenv, version 9.0.3

real    0m0.704s
user    0m0.653s
sys     0m0.052s 
```

Enter fullscreen mode Exit fullscreen mode

安装轮子后，pip 构建轮子并从中安装。

从车轮安装(。whl)和来自源包(. tar.gz)是不同的过程。
从 wheel 安装时，脚本中不使用 pkg _ resources:

```
$ cat local/py37/bin/pipenv
#!/home/inada-n/local/py37/bin/python3.7

# -*- coding: utf-8 -*-
import re
import sys

from pipenv import cli

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(cli()) 
```

Enter fullscreen mode Exit fullscreen mode

### IPython

参见下一部分。

```
import time:       385 |     236655 |             IPython
import time:        22 |     236677 |           IPython.core
import time:        26 |     236703 |         IPython.core.magic
import time:       978 |     237680 |       dotenv.ipython
import time:       199 |     239032 |     dotenv 
```

Enter fullscreen mode Exit fullscreen mode

`pipenv`进口`dotenv`，`dotenv`进口`dotenv.ipython`，它进口`IPython`。

这就是为什么`pipenv`在我的环境中启动缓慢；我安装了 IPython。

但是为什么要导入 IPython 呢？我读了 dotenv 源代码，发现它是 IPython 的扩展。

当然，pipenv 和许多 dotenv 用户不使用 IPython 扩展。
我向 dotenv 发出了[拉请求](https://github.com/theskumar/python-dotenv/pull/84),要求按需导入 IPython。

由于 pipenv 有自己的 dotenv 副本，所以我向 pipenv 发出了[拉请求](https://github.com/pypa/pipenv/pull/1326)，从而完全删除了`dotenv.ipython`。

## 结论

我可以将`pipenv --version`的时间从 800 毫秒减少到 500 毫秒。

```
$ time local/py37/bin/pipenv --version
pipenv, version 9.0.3

real    0m0.503s
user    0m0.463s
sys     0m0.040s 
```

Enter fullscreen mode Exit fullscreen mode

导入时间分析是研究和优化应用程序启动时间的非常好的方法。