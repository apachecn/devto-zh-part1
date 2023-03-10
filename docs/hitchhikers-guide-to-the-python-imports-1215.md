# Python 导入的搭便车指南

> 原文：<https://dev.to/dzeban/hitchhikers-guide-to-the-python-imports-1215>

**免责声明**:如果你每天都写 Python，你在这篇文章中不会发现什么新东西。它是为那些**偶尔像 Ops guys 一样使用 Python** 却忘记/误用其导入系统的人准备的。尽管如此，代码是用 Python 3.6 类型注释编写的，以取悦有经验的 Python 读者。像往常一样，如果你发现任何错误，请让我知道！

## 模块

让我们从一个常见的 Python 节
开始

```
if __name__ == '__main__':
    invoke_the_real_code() 
```

Enter fullscreen mode Exit fullscreen mode

很多人，我也不例外，把它写成一种仪式，而没有试图去理解它。我们多少知道，当您从 CLI 调用代码与导入代码时，这个代码片段会有所不同。但是让我们试着理解为什么我们真的需要它。

举例来说，假设我们正在编写一些比萨饼店软件。Github 上的[。这里是`pizza.py`文件。](https://github.com/dzeban/python-imports) 

```
# pizza.py file 
import math

class Pizza:
    name: str = ''
    size: int = 0
    price: float = 0

    def __init__(self, name: str, size: int, price: float) -> None:
        self.name = name
        self.size = size
        self.price = price

    def area(self) -> float:
        return math.pi * math.pow(self.size / 2, 2)

    def awesomeness(self) -> int:
        if self.name == 'Carbonara':
            return 9000

        return self.size // int(self.price) * 100

print('pizza.py module name is %s' % __name__)
if __name__ == '__main__':
    print('Carbonara is the most awesome pizza.') 
```

Enter fullscreen mode Exit fullscreen mode

我添加了神奇的`__name__`变量的打印，看看它会如何变化。

好的，首先，让我们把它作为一个脚本来运行:

```
$ python3 pizza.py
pizza.py module name is __main__
Carbonara is the most awesome pizza. 
```

Enter fullscreen mode Exit fullscreen mode

事实上，当我们从 CLI 调用全局变量`__name__`时，它被设置为`__main__`。

但是如果我们从另一个文件导入呢？下面是`menu.py`的源代码:

```
# menu.py file 
from typing import List
from pizza import Pizza

MENU: List[Pizza] = [
    Pizza('Margherita', 30, 10.0),
    Pizza('Carbonara', 45, 14.99),
    Pizza('Marinara', 35, 16.99),
]

if __name__ == '__main__':
    print(MENU) 
```

Enter fullscreen mode Exit fullscreen mode

运行`menu.py`

```
$ python3 menu.py
pizza.py module name is pizza
[<pizza.Pizza object at 0x7fbbc1045470>, <pizza.Pizza object at 0x7fbbc10454e0>, <pizza.Pizza object at 0x7fbbc1045b38>] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们看到两件事:

1.  pizza.py 中的顶级`print`语句在导入时执行
2.  pizza.py 中的`__name__`现在被设置为不带`.py`后缀的文件名。

因此，`__name__`是保存当前 Python 模块名称的全局变量。

*   模块名由解释器在`__name__`变量中设置
*   当从 CLI 调用模块时，其名称被设置为`__main__`

那么，到底是什么模块呢？这真的很简单——module 是一个包含 Python 代码的文件，您可以用解释器(`python`程序)执行它，或者从其他模块导入它。

*   Python 模块只是一个带有 Python 代码的文件

就像在执行时一样，当模块被导入时，它的顶层语句被执行，但是要注意，即使你从不同的文件导入它几次，它也只会被执行一次。

*   当你导入模块时，它被执行

因为模块只是普通文件，所以有一个简单的方法来导入它们。只需取文件名，去掉`.py`扩展名，并把它放在`import`语句中。

*   要导入模块，可以使用不带扩展名`.py`的文件名

有趣的是,`__name__`被设置为文件名，不管你如何导入它——而`import pizza as broccoli` `__name__`仍然是`pizza`。因此

*   导入时，模块名称被设置为不带`.py`扩展名的文件名，即使它是用`import module as othername`重命名的

但是如果我们导入的模块不在同一个目录中，我们该如何导入呢？答案在模块搜索路径中，我们将在讨论包时最终发现它。

## 套餐

*   包是模块集合的名称空间

名称空间部分很重要，因为包本身并不提供任何功能——它只给你一种方法来分组你的模块。

有两种情况下，你真的想把模块放到一个包里。首先是将一个模块的定义与另一个模块的定义隔离开来。在我们的`pizza`模块中，我们有一个`Pizza`类，它可能会与其他人的披萨包发生冲突([，我们确实在 pypi](https://pypi.org/search/?q=pizza) 上有一些披萨包

第二种情况是，如果你想分发你的代码，因为

*   包是 Python 中代码*分发*的最小单位

你在 PyPI 上看到并通过`pip`安装的所有东西都是一个包，所以为了分享你的好东西，你必须用它做一个包。

好吧，假设我们被说服了，想把我们的 2 个模块转换成一个不错的包。为此，我们需要创建一个包含空`__init__.py`文件的目录，并将我们的文件移动到这个目录中:

```
pizzapy/
├── __init__.py
├── menu.py
└── pizza.py 
```

Enter fullscreen mode Exit fullscreen mode

就这样——现在您有了一个`pizzapy`包！

*   用`__init__.py`文件创建一个包目录

请记住，包是模块的名称空间，所以您不需要导入包本身，而是从包中导入一个模块。

```
>>> import pizzapy.menu
pizza.py module name is pizza
>>> pizzapy.menu.MENU
[<pizza.Pizza object at 0x7fa065291160>, <pizza.Pizza object at 0x7fa065291198>, <pizza.Pizza object at 0x7fa065291a20>] 
```

Enter fullscreen mode Exit fullscreen mode

如果以这种方式进行导入，可能会显得太冗长，因为您需要使用完全限定名。我猜想这是有意的行为，因为 Python Zen 的一条是“显式比隐式好”。

无论如何，你总是可以用一个`from package import module`形式来缩短名字:

```
>>> from pizzapy import menu
pizza.py module name is pizza
>>> menu.MENU
[<pizza.Pizza object at 0x7fa065291160>, <pizza.Pizza object at 0x7fa065291198>, <pizza.Pizza object at 0x7fa065291a20>] 
```

Enter fullscreen mode Exit fullscreen mode

### 包初始化

还记得我们如何将一个`__init__.py`文件放在一个目录中，然后它神奇地变成了一个包吗？这是约定优于配置的一个很好的例子——我们不需要描述任何配置或注册任何东西。任何带有`__init__.py`的目录按照惯例都是一个 Python 包。

除了制作一个包之外,`__init__.py`还传达了一个目的——包初始化。这就是为什么它最终被称为 init！包导入时触发初始化，换句话说，导入包调用`__init__.py`

*   导入包时，会执行包的`__init__.py`模块

在`__init__`模块中，你可以做任何你想做的事情，但最常见的是用于一些包初始化或设置特殊的`__all__`变量。后者控制着明星的进口——`from package import *`。

因为 Python 很棒，我们可以在`__init__`模块中做几乎任何事情，甚至是非常奇怪的事情。假设我们不喜欢 import 的显式性，并希望将所有模块的符号拖到包级别，这样我们就不必记住实际的模块名。

要做到这一点，我们可以从`__init__.py`中的`menu`和`pizza`模块导入所有东西，就像这样

```
# pizzapy/__init__.py

from pizzapy.pizza import *
from pizzapy.menu import * 
```

Enter fullscreen mode Exit fullscreen mode

参见:

```
>>> import pizzapy
pizza.py module name is pizzapy.pizza
pizza.py module name is pizza
>>> pizzapy.MENU
[<pizza.Pizza object at 0x7f1bf03b8828>, <pizza.Pizza object at 0x7f1bf03b8860>, <pizza.Pizza object at 0x7f1bf03b8908>] 
```

Enter fullscreen mode Exit fullscreen mode

没有更多的`pizzapy.menu.Menu`或`menu.MENU` :-)，它的工作方式有点像 Go 中的包，但请注意，这是不鼓励的，因为你试图滥用 Python，如果你要签入这样的代码，你将在代码审查中度过一段糟糕的时间。我给你看这个只是为了说明，不要怪我！

您可以像这样更简洁地重写导入

```
# pizzapy/__init__.py

from .pizza import *
from .menu import * 
```

Enter fullscreen mode Exit fullscreen mode

这只是做同样事情的另一种语法，叫做相对导入。让我们仔细看看。

### 绝对和相对进口

上面的两段代码是进行所谓的相对导入的唯一方式，因为 Python 3 默认情况下所有的导入都是绝对的(如 [PEP328](https://docs.python.org/2.5/whatsnew/pep-328.html) )，这意味着导入将首先尝试导入标准模块，然后才是本地包。当你创建自己的`sys.py`模块时，这是为了避免标准模块的阴影，这样做`import sys`会覆盖标准库`sys`模块。

*   因为 Python 3 默认情况下所有导入都是绝对的——它将首先寻找系统包

但是如果你的包中有一个名为`sys`的模块，你想把它导入到同一个包的另一个模块中，你必须做一个**相对导入**。要做到这一点，你必须再次明确，写`from package.module import
somesymbol`或`from .module import somesymbol`。模块名前那个有趣的单点读作“当前包”。

*   要进行相对导入，请在模块前添加包名或点

### 可执行包

在 Python 中，你可以用一个`python3 -m <module>`结构调用一个模块。

```
$ python3 -m pizza
pizza.py module name is __main__
Carbonara is the most awesome pizza. 
```

Enter fullscreen mode Exit fullscreen mode

但是包也可以这样调用:

```
$ python3 -m pizzapy
/usr/bin/python3: No module named pizzapy. __main__ ; 'pizzapy' is a package and cannot be directly executed 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它需要一个`__main__`模块，所以我们来实现它:

```
# pizzapy/__main__.py

from pizzapy.menu import MENU

print('Awesomeness of pizzas:')
for pizza in MENU:
    print(pizza.name, pizza.awesomeness()) 
```

Enter fullscreen mode Exit fullscreen mode

现在它工作了:

```
$ python3 -m pizzapy
pizza.py module name is pizza
Awesomeness of pizzas:
Margherita 300
Carbonara 9000
Marinara 200 
```

Enter fullscreen mode Exit fullscreen mode

*   添加`__main__.py`使包可执行(用`python3 -m package`调用它)

### 导入同级包

我想介绍的最后一件事是兄弟包的导入。假设我们有一个兄弟包`pizzashop` :

```
.
├── pizzapy
│   ├── __init__.py
│   ├── __main__.py
│   ├── menu.py
│   └── pizza.py
└── pizzashop
    ├── __init__.py
    └── shop.py

# pizzashop/shop.pyimport pizzapy.menuprint(pizzapy.menu.MENU) 
```

Enter fullscreen mode Exit fullscreen mode

现在，在顶级目录中，如果我们尝试像这样调用 shop.py】

```
$ python3 pizzashop/shop.py
Traceback (most recent call last):
  File "pizzashop/shop.py", line 1, in <module>
    import pizzapy.menu
ModuleNotFoundError: No module named 'pizzapy' 
```

Enter fullscreen mode Exit fullscreen mode

我们得到错误，我们的 pizzapy 模块没有找到。但是如果我们调用它作为包的一部分

```
$ python3 -m pizzashop.shop
pizza.py module name is pizza
[<pizza.Pizza object at 0x7f372b59ccc0>, <pizza.Pizza object at 0x7f372b59ccf8>, <pizza.Pizza object at 0x7f372b59cda0>] 
```

Enter fullscreen mode Exit fullscreen mode

突然起作用了。这到底是怎么回事？

对此的解释在于 Python 模块搜索路径，并且在关于模块的文档[中有详细描述。](https://docs.python.org/3/tutorial/modules.html#the-module-search-path)

模块搜索路径是解释器用来定位模块的目录列表(在运行时作为`sys.path`可用)。它被初始化为 Python 标准模块的路径(`/usr/lib64/python3.6`)、`site-packages`，其中`pip`放置你全局安装的所有东西，还有一个依赖于你如何运行一个模块的目录。如果你像`python3 pizzashop/shop.py`一样运行一个模块，包含目录的路径(`pizzashop`)被添加到`sys.path`。否则，包括使用`-m`选项运行，当前目录(如`pwd`所示)被添加到模块搜索路径。我们可以通过在`pizzashop/shop.py` :
中打印`sys.path`来检查

```
$ pwd
/home/avd/dev/python-imports

$ tree
.
├── pizzapy
│   ├── __init__.py
│   ├── __main__.py
│   ├── menu.py
│   └── pizza.py
└── pizzashop
    ├── __init__.py
    └── shop.py

$ python3 pizzashop/shop.py
['/home/avd/dev/python-imports/pizzashop',
 '/usr/lib64/python36.zip',
 '/usr/lib64/python3.6',
 '/usr/lib64/python3.6/lib-dynload',
 '/usr/local/lib64/python3.6/site-packages',
 '/usr/local/lib/python3.6/site-packages',
 '/usr/lib64/python3.6/site-packages',
 '/usr/lib/python3.6/site-packages']
Traceback (most recent call last):
  File "pizzashop/shop.py", line 5, in <module>
    import pizzapy.menu
ModuleNotFoundError: No module named 'pizzapy'

$ python3 -m pizzashop.shop
['',
 '/usr/lib64/python36.zip',
 '/usr/lib64/python3.6',
 '/usr/lib64/python3.6/lib-dynload',
 '/usr/local/lib64/python3.6/site-packages',
 '/usr/local/lib/python3.6/site-packages',
 '/usr/lib64/python3.6/site-packages',
 '/usr/lib/python3.6/site-packages']
pizza.py module name is pizza
[<pizza.Pizza object at 0x7f2f75747f28>, <pizza.Pizza object at 0x7f2f75747f60>, <pizza.Pizza object at 0x7f2f75747fd0>] 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，在第一种情况下，我们的路径中有`pizzashop`目录，因此我们找不到兄弟`pizzapy`包，而在第二种情况下，当前目录(表示为`''`)在`sys.path`中，它包含两个包。

*   Python 有运行时可用的模块搜索路径`sys.path`
*   如果以脚本文件形式运行模块，则包含目录被添加到`sys.path`中，否则当前目录被添加到其中

当人们将一堆测试或示例脚本放在主包旁边的目录或包中时，经常会出现导入兄弟包的问题。这里有几个 StackOverflow 问题:

*   [https://stackoverflow.com/q/6323860](https://stackoverflow.com/q/6323860)
*   [https://stackoverflow.com/q/6670275](https://stackoverflow.com/q/6670275)

好的解决方案是避免这个问题——将测试或例子放在包中，并使用相对导入。肮脏的解决方法是在运行时修改`sys.path`(耶，动态！)通过添加所需包的父目录。尽管这是一个可怕的黑客行为，人们还是会这么做。

## 结束！

我希望读完这篇文章后，你会对 Python 导入有更好的理解，并最终可以毫无畏惧地分解你工具箱中的巨大脚本。最后，Python 中的一切都非常简单，甚至当它不足以满足您的情况时，您也可以在运行时用猴子修补任何东西。

说到这里，我想停下来，感谢大家的关注。下次见！