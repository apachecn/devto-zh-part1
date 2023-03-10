# Python 虚拟环境的终极解决方案:pyenv + virtualenvwrapper

> 原文：<https://dev.to/alysivji/ultimate-solution-to-python-virtual-environments-pyenv--virtualenvwrapper>

(本文最初发表于 [Siv 脚本](https://alysivji.github.io/)

我喜欢使用命令行；这是我最喜欢的编程部分。事情并不总是这样。我曾经担心我输入的命令会破坏微妙的平衡，在这种平衡中，所有的东西都可以正常工作(T2)。这会是结束这一切的`pip install`吗？

进入虚拟环境。

虚拟环境(venv)是一种工具，它允许我们保持项目依赖性的独立性。如果项目 A 需要 pandas 版本 0.17，项目 B 需要 pandas 版本 0.21，我们可以为每个项目创建一个 venv，并保持工作流独立。更多信息见 [Python 文档](https://docs.python.org/3/library/venv.html#venv-def)。

自从我开始在我的项目中使用虚拟环境已经有一年了，但是我还没有找到完美的开发工作流程。

试炼[蟒蛇](https://www.continuum.io/what-is-anaconda)；对于非数据科学的工作来说，它太臃肿了。

接下来是[virtualenv](https://virtualenv.pypa.io/en/stable/)+[virtualevnwrapper](http://virtualenvwrapper.readthedocs.io/en/latest/)。做了我想做的一切，但是在为不同版本设置环境时，我必须手动管理 Python 安装。

像每个学习 Docker 的人一样，我经历了一个[将所有东西容器化的阶段](https://alysivji.github.io/containerized-development-environments.html)。我的 Mac 上不存在的电池寿命告诉我这不是最可行的解决方案。

然后我找到了 pyenv...

[![Just Right](img/aaa0e42d9a8a9f15cf9e2a39faafea68.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--Klffbn_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alysivji.github.img/11-20/13_penv_just_right_meme.jpg)

在这篇文章中，我将描述如何安装 pyenv 和 pyenv-virtualenvwrapper。我还将介绍设置和使用新虚拟环境所需的步骤。

* * *

## pyenv

> [pyenv 让你可以在多个版本的 Python 之间轻松切换](https://github.com/pyenv/pyenv#simple-python-version-management-pyenv)

### 背景

每当我们运行一个命令时，操作系统都会在环境变量`$PATH`的目录列表中寻找可执行文件。

```
$  echo $PATH
/Users/alysivji/.local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/alysivji/bin $  which python
/usr/bin/python $  which python3
/usr/local/bin/python3 
```

Enter fullscreen mode Exit fullscreen mode

我们看到默认的 Python 和 Python3.6 安装都位于`$PATH` var 中。

当我们安装 pyenv 时，我们在我们的`$PATH`前面添加了一个额外的[垫片](https://en.wikipedia.org/wiki/Shim_(computing))目录。每个 shim 拦截命令(`python`、`pip`等)，并将它们重定向到适当的位置。

### 安装

查看该项目的 Github 获取详细的安装说明。这些说明假设你已经安装了 macOS + [家酿](https://brew.sh/)。

```
brew update
brew install pyenv 
```

Enter fullscreen mode Exit fullscreen mode

在`~/.bash_profile`中添加以下一行:

```
eval "$(pyenv init -)" 
```

Enter fullscreen mode Exit fullscreen mode

让我们确保我们的安装工作正常:

```
$  source ~/.bash_profile
$  echo $PATH
/Users/alysivji/.pyenv/shims:/Users/alysivji/.local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/alysivji/bin 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错！我们的`$PATH`变量前面的垫片将拦截和重定向所有 python 相关的命令。

### 命令

我们使用`pyenv install --list`来获得所有可供安装的 Python 版本的列表。让我们安装几个不同版本的 Python，以便了解 pyenv 是如何工作的。

```
pyenv install 2.7.13
pyenv install pypy-5.7.1
pyenv install 3.6.2
pyenv install 3.7-dev 
```

Enter fullscreen mode Exit fullscreen mode

将我们想要的 Python 设置为默认版本:

```
$  pyenv global 3.6.2
$  pyenv versions
 system
  2.7.13
* 3.6.2 (set by /Users/alysivji/.pyenv/version)
  3.7-dev
  pypy-5.7.1 
```

Enter fullscreen mode Exit fullscreen mode

shim 如何知道使用哪个版本的 Python？它沿着[层级](https://github.com/pyenv/pyenv#choosing-the-python-version)向下，直到找到它要找的东西。

使用`pyenv shell [version]`命令激活不同版本的 Python。

```
$  which python
/Users/alysivji/.pyenv/shims/python $  python
Python 3.6.2 (default, Aug 24 2017, 00:00:01)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information. >>> exit()
$  pyenv shell 2.7.13
$  which python
/Users/alysivji/.pyenv/shims/python $  python
Python 2.7.13 (default, Aug 24 2017, 00:14:24)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information. >>> 
```

Enter fullscreen mode Exit fullscreen mode

请注意 pyenv shims 如何为我们打理一切。接下来，我们将学习如何创建一个`venv`。

* * *

## pyenv-虚拟包装

> pyenv-virtualenvwrapper 是一个 pyenv 插件...使用 virtualenvwrapper 管理您的 virtualenvs】

### 安装

详细的[说明](https://github.com/pyenv/pyenv-virtualenvwrapper)在 Github 上。macOS + [家酿](https://brew.sh/)用户可以跟随:

```
brew install pyenv-virtualenvwrapper 
```

Enter fullscreen mode Exit fullscreen mode

追加到`~/.bash_profile`:T1

```
export PYENV_VIRTUALENVWRAPPER_PREFER_PYVENV="true" export WORKON_HOME=$HOME/.virtualenvs
pyenv virtualenvwrapper_lazy 
```

Enter fullscreen mode Exit fullscreen mode

重装外壳:

```
source ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

### 命令

每当我们安装新版本的 Python 时，我们都需要安装 virtualenvwrapper。这可以通过`pyenv virtualenvwrapper`或`pyenv virtualenvwrapper_lazy` ( **首选**)命令:
来完成

```
$  pyenv shell 3.6.2
$  pyenv virtualenvwrapper_lazy
Collecting virtualenvwrapper
Collecting stevedore (from virtualenvwrapper)
  Using cached stevedore-1.25.0-py2.py3-none-any.whl
Collecting virtualenv (from virtualenvwrapper)
  Using cached virtualenv-15.1.0-py2.py3-none-any.whl
Collecting virtualenv-clone (from virtualenvwrapper)
  Using cached virtualenv-clone-0.2.6.tar.gz Collecting pbr!=2.1.0,>=2.0.0 (from stevedore->virtualenvwrapper)
 Using cached pbr-3.1.1-py2.py3-none-any.whl Collecting six>=1.9.0 (from stevedore->virtualenvwrapper)
 Using cached six-1.10.0-py2.py3-none-any.whl
Installing collected packages: pbr, six, stevedore, virtualenv, virtualenv-clone, virtualenvwrapper
  Running setup.py install for virtualenv-clone ... done
Successfully installed pbr-3.1.1 six-1.10.0 stevedore-1.25.0 virtualenv-15.1.0 virtualenv-clone-0.2.6 virtualenvwrapper-4.7.2 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用 [virtualenvwrapper shell 命令](http://virtualenvwrapper.readthedocs.io/en/latest/command_ref.html)来管理我们的环境:

```
$  mkvirtualenv venv_test
Using base prefix '/Users/alysivji/.pyenv/versions/3.6.2'
New python executable in /Users/alysivji/.virtualenvs/venv_test/bin/python3.6
Also creating executable in /Users/alysivji/.virtualenvs/venv_test/bin/python
Installing setuptools, pip, wheel...done.
virtualenvwrapper.user_scripts creating /Users/alysivji/.virtualenvs/venv_test/bin/predeactivate
virtualenvwrapper.user_scripts creating /Users/alysivji/.virtualenvs/venv_test/bin/postdeactivate
virtualenvwrapper.user_scripts creating /Users/alysivji/.virtualenvs/venv_test/bin/preactivate
virtualenvwrapper.user_scripts creating /Users/alysivji/.virtualenvs/venv_test/bin/postactivate
virtualenvwrapper.user_scripts creating /Users/alysivji/.virtualenvs/venv_test/bin/get_env_details (venv_test) $  which python
/Users/alysivji/.virtualenvs/venv_test/bin/python (venv_test) $  python
Python 3.6.2 (default, Aug 24 2017, 00:00:01)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information. >>> 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用`deactivate`和`workon`命令在虚拟环境之间切换:

```
(venv_test) $  deactivate
$  workon venv_test
(venv_test) $  python
Python 3.6.2 (default, Aug 24 2017, 00:00:01)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.42.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information. >>> 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样。我们可以用两个命令为任何版本的 Python 创建一个虚拟环境。

#### 备注

*   `virtualenvwrapper_lazy` [减少炮弹装载时间](https://www.reddit.com/r/Python/comments/11e773/tip_virtualenvwrapper_has_a_lazy_version_you_can/)

* * *

## 结论

我们应该一直寻找优化我们开发工作流程的方法。有很多工具可以帮助我们管理复杂性，让我们的生活变得简单。找到这些工具。使用它们。从长远来看，这会让你更有效率。

* * *

##### 附加资源

*   [虚拟环境上的真实 Python 入门](https://realpython.com/blog/python/python-virtual-environments-a-primer/)
*   [virtualenvwrapper 命令列表](http://virtualenvwrapper.readthedocs.io/en/latest/command_ref.html)
*   [pyenv 命令参考](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md)
*   pyenv-virtualenv-pyenv 的替代虚拟环境管理器
*   [direnv](https://direnv.net/)