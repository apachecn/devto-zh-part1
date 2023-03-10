# 您自己的 Python REPL，不超过 20 行

> 原文：<https://dev.to/rpalo/your-own-python-repl-in-twenty-lines-or-less>

你是否曾经在 Python 交互式 REPL 中对自己说，“你知道，这很好，但是...它少了点什么。不够*我*！”好了，不用担心了！我将用 20 行或更少的代码演示给你看。我来介绍一下主要景点:

## 代码

虽然听起来没什么用，但这是真的。Python 在标准库中有一个模块叫做`Code`。不信你可以看看[代码模块文档](https://docs.python.org/3/library/code.html)。文档在第一次传递时可能有点难以理解，所以我想我应该创建几个例子来展示一下。在`code`模块中有一些对象，但是这次我只讨论一个:`interact`。

## 互动

让我们开始吧。我带你去看。让我们在目录中创建新文件`shell.py`。

```
#!/usr/bin/env python
# I'll be using Python 3, for reference. 
import code  # seems silly, but works great 
def custom_repl():
    pie = "delicious"
    pi = 3.14159
    homonyms = (pie != pi)
    code.interact(
        banner="Hello!  My name is Jeeves!  I am fancy.  ðŸŽ©",
        local=locals(),
        exitmsg="Ta-ta!"
    )

if __name__ == "__main__":
    custom_repl() 
```

Enter fullscreen mode Exit fullscreen mode

尝试运行它，看看神奇的工作。

```
$ python3 shell.py
Hello!  My name is Jeeves!  I am fancy.  ðŸŽ©
>>> pi
3.1415
>>> pie
'delicious'
>>> homonyms
True
>>> 4 + 3
7
>>> import math
>>> math.sqrt(pi)
1.7724531023414978
<Ctrl-D>
Ta-ta! 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们指定的横幅消息显示，我们指定的退出消息显示(虽然它对我来说不工作，如果我使用内置的`exit()`或`quit()`命令)，当`interact`被调用时，我们可以访问本地变量。我们可以使用任何东西作为我们的本地状态！返回并创建`shell2.py`。

```
#!/usr/bin/env python
# shell2.py 
import code

class Car:
    def __init__(self, color, mileage):
        self.color = color
        self.mileage = mileage

    def honk(self):
        return "Beep!"

    def __repr__(self):
        return "<Car - color: {}, mileage: {}>".format(self.color, self.mileage)

if __name__ == "__main__":
    c = Car("red", 20000)
    code.interact(
        banner="Now interactively inspecting {}".format(c),
        local={"car": c},
    ) 
```

Enter fullscreen mode Exit fullscreen mode

回到壳里...`python3 shell2.py`

```
Now interactively inspecting <Car - color: red, mileage: 20000>
>>> car
<Car - color: red, mileage: 20000>
>>> car.honk()
'Beep!' 
```

Enter fullscreen mode Exit fullscreen mode

请记住，`interact`的`local`参数必须是 Dict——比如由`local()`和`global()`返回的参数。

## 总结起来

您可以使用它来为您的应用程序提供一个良好的交互方面，以帮助您做 shell 工作、检查对象、调试等等。这也是 [pdb](https://docs.python.org/3/library/pdb.html) 的大致工作方式！Django 和 Flask 都在他们的“shell”命令中使用了这个版本。我想我会试着用它来为 python 介绍类生成一个自动化的测验或作业(类似于 R 中的[漩涡包)。你还有其他很酷的使用方式吗？让我知道！](https://www.youtube.com/watch?v=McoHQIxJd-o)

* * *

*最初发布于[我的博客](http://assertnotmagic.com)T3】*