# python？python？但从何说起呢？

> 原文：<https://dev.to/gabrielaraujof/python-mas-por-onde-comeco-4b85>

> 你为什么要学 Python？

答案比较简单，正如在重写的一篇文章中详述的那样。2008 年以来 Python 被评为世界上八种最流行的编程语言之一。其中很大一部分是由于谷歌、雅虎和 NASA 等大型技术公司广泛采用 Python。但并不仅仅是市场指明并推动了这样的流行。美国十大计算机学院中有八所目前在编程入门学科教授 Python。这是因为与大多数语言相比，语言学习曲线很小。

此外，我们还有另一个原因:在任何计算机上开始用 Python 编程所需的设置是 ***【非常】**【简单】*。那就别说了，我们去找最重要的！**

<figure>[![fonte: [https://www.python.org/community/logos/](https://www.python.org/community/logos/)](img/a1f3e07187bcbd0e99d6826145b1971b.png "fonte: [https://www.python.org/community/logos/](https://www.python.org/community/logos/)")](///static/3c3b709e32ba6c1949cd765c0eddcce9/8ae3e/asset-1.png)

<figcaption>font:[https://www.python.org/community/logos/](https://www.python.org/community/logos/)</figcaption>

</figure>

### 巨蟒

#### Linux

大多数 GNU/Linux 系统已经预先安装 Python，所以在这个问题上你绝对没有什么可以做的—太好了！但是，其他一些仅提供 python 2 . x 版。如果您更喜欢 3.x 版，则需要安装它，但这相当简单。(*强烈建议使用版本 3。版本 2 将在* [*【简短】*](https://pythonclock.org/) *中停止。*

*注:这一逐步的步骤是基于 Debian 派生的分布。对于其他发行版，您需要在安装 Python 之前对其进行编译。*

打开终端并运行以下命令:

```
 $ sudo apt-get update && sudo apt-get install python3 
```

**准备好了，您已经安装 Python 3，准备使用！**在同一终端上运行 python3 命令，您将进入 python 交互模式。你会看到类似:

```
 $ python3

Python 3.4.0 (default, Apr 11 2014, 13:05:11) [GCC 4.8.2] on linux Type “help”, “copyright”, “credits” or “license” for more information.

>>> 
```

从此处，您可以运行 Python 命令。要退出交互模式，只需运行*【quit()*。

#### 麦克 OSX

与 GNU/Linux 系统一样，python 2 . x 版已经预先安装在 Mac OSX 上。如果您要使用 3.x 版，则需要安装它。下载适用于 MAC OS x 的最新版本的“[python 3 . x”。](https://www.python.org/downloads/)

然后，双击下载的文件“**”。pkg“**”开始安装(“T2”[您可能需要输入管理员用户名和密码“”。按照屏幕上的说明进行操作，接受许可条款并确认安装。

**准备好了，Python 3 已经准备好在 Mac 上使用！**浏览*应用程序>实用程序*并运行终端。在终端屏幕上运行 python3 命令，该命令将引导您进入 python 交互模式，您可以在该模式下运行您的命令。你会看到类似这样的东西:

```
 $ python3

Python 3.4.1 (default, Apr 11 2014, 13:05:11) [GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin Type “help”, “copyright”, “credits” or “license” for more information.

>>> 
```

要退出交互模式并返回终端，只需运行功能*【quit()*。

#### 视窗

windows 未出厂预装 Python，因此需要手动安装。下载适用于 windows 的最新版本的[python 3 . x 安装程序。](https://www.python.org/downloads/)

*注意:选择适合您 Windows 架构的安装程式:x86 (32 位元)或 x86–64(64 位元)*。

双击下载的安装程序开始安装。按照说明进行操作，等待安装过程完成。**准备好了，您的 Windows 已经安装 Python 了！** *重要*:如果要安装 python 2 . x 版，很可能需要配置环境变量。对于 3.x 版，安装程序已自动执行此步骤。一经安装，您就可以通过 Python 编程来享受乐趣。只需运行命令提示并进入交互模式。

```
 C:\Users\Gabriel Araujo> python

Python 3.4.2 (v3.4.2:ab2c023a9432, Apr 11 2014, 13:05:11) [MSC v.1600 64 bit (AMD64)] on win32 Type "help", "copyright", "credits" or "license" for more information.

>>> 
```

### 使用交互模式！

> **提示:T1】主要是对开始编程的人来说，python(或 shell)的交互模式非常重要。您可以使用它来执行命令，从而立即获得执行反馈。**

例如，交互模式对于执行某些命令非常有用—您知道何时不记得语法，还是不知道语法是否真的有用？因此，在交互模式下只运行这一段代码，您就会立即得到响应。例如下例:

```
 >>> print 'Olá, mundo!'

  File "<stdin>", line 1

    print 'Olá, mundo!'

                      ^

SyntaxError: invalid syntax

>>> print('Olá, mundo!')

Olá, mundo!

>>> 
```

* * *

*最初发表于 2015 年 3 月 21 日*[*gabrielaraujo . dev*](https://www.gabrielaraujo.dev/posts/python-mas-por-onde-comeco/)*。*