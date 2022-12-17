# pex — python executables

> 原文：<https://dev.to/ineedale/pex-python-executables-4fcf>

ython **EX** 可执行程序是 ***棒极了*** ，它们带来了你运行代码所需的一切。就像 virtualenv 以前做的那样，但不需要初始化环境或做我最近开始更经常使用的 pip 路径导入(因为我不想分发整个环境)

简而言之，你用
安装你所有的需求

```
pip install -t .pip -r requirements.txt 
```

所有模块都将安装到。pip，您可以在将路径添加到 PYTHONPATH 时使用它。你可以在你的应用程序/脚本中做以下事情

```
#!/usr/bin/env python

import sys
import os

sys.path.append(sys.path.append(os.path.abspath('.pip')))

import your.modules.from.requirements.txt 
```

所以，以上并不太坏，你可以相对容易地分发。pip 目录，而不需要整个环境，您也不需要在目标机器或容器(或环境)上安装任何要求。

做到以上几点还不错，但是我们可以做得更好！这就是 pex 的用武之地，它是一个 python 可执行文件，可以通过使用 pants (python ant)或 PEX 模块来构建。在本例中，我们将使用 pex 模块。

### 安装 pex

```
$ (sudo) pip install pex 
```

是的，这就是安装 pex 所要做的全部工作；)

### 构建一个. pex 文件

所以不像 virtualenv 和。pip hack，您不能简单地添加一个 python 脚本并将其作为。pex 文件，正如你(或我)可能已经预料到的。

构建一个. pex 文件，其功能就像 virtualenv 或。pip 安装了我们的所有要求后，您可以执行以下简短步骤，确保您首先安装了 pex(见上文)。

```
$ pex module1 module2 module3 -o myenv.pex 
```

要使用这个 myenv.pex(现在已经有了所有需要的模块),只需运行

```
$ ./myenv.pex 
```

默认情况下，这将只是启动 python 本身，然后您可以导入您已经添加到。pex 文件，或者运行一个脚本，做一个简单的

```
$ ./myenv.pex script.py 
```

使用 requirements.txt 文件可能更容易创建您的环境

```
$ pex -r requirements.txt -o myenv.pex 
```

### 在。pex，不是作为一个脚本

所以，结果是你可以在。pex 文件，它只是比你想要的多一点点工作。是不是很棒！？想象一个 go 静态二进制，但它是 python。

因此，让我们假设您想要运行一个打印 Hello！当执行时，显然不需要它自己的环境，等等。但这只是一个练习。

#### __init__。py，hello.py & setup.py

首先，我们需要创建必要的目录结构和文件，使其成为可安装的 python 模块/包。为此，我们将需要以下的

```
./hello/__init__.py ./hello/setup.py ./hello/hello.py 
```

__init__。py 可以是空的-耶！

setup.py 确实需要两行样板文件

```
from distutils.core import setup
setup( name='hello', version='0.0.1', py_modules=['hello'] ) 
```

py 会做我们要求它做的事情——打印 hello

```
def hello(): 
 print "hello!" 
```

是的，这正是我们所需要的——很棒，不是吗？

#### 建造了。pex 文件

从“hello”模块/包/路径之外，运行 pex 来创建 pex

```
$ pex hello -e hello:hello -o hello.pex 
```

这将把我们创建的“hello”模块安装到。pex 文件中的-e hello:hello 声明了一个入口点，它将加载`module "hello"`并在调用。pex 正在运行。

### 最终结果

输出

```
vagrant@local:/vagrant$ pex hello -e hello:hello -o hello.pex

vagrant@local:/vagrant$ ls
 hello 
 hello.pex

vagrant@local:/vagrant$ ./hello.pex
hello! 
```

文件/目录大小比较

*   **。安装了 ansible 的 pex 文件:3 MB**
*   。安装了 ansible 的 pip 目录:17 MB
*   安装 ansible 的 virtualenv:28 MB

* * *