# 计时您的 Python 代码

> 原文：<https://dev.to/cnu/timing-your-python-code>

很多时候你会想知道程序执行需要多少时间。在 unix 系统上，最简单的方法是在运行程序之前使用`time`命令。

```
$ time command

real    0m1.320s
user    0m0.010s
sys     0m0.023s 
```

Enter fullscreen mode Exit fullscreen mode

这表明`command`花费了 1.32 秒的挂钟时间，0.01 秒的用户空间时间和 0.023 秒的内核空间时间。

这适用于您可以在 shell 上运行的任何命令。但是你怎么知道一个 python 函数需要多长时间呢？或者某一行代码需要多长时间？

我通常的做法是在这段代码之前创建一个`time.time()`对象，将它存储在一个变量中。做同样的`time.time()`，从先前存储的值中减去并打印出来。

没毛病。但是，与其到处重复同样的代码，一定有更好的方法。

让我介绍一下计时上下文管理器。

创建一个名为`timer.py`的模块，并将下面这段代码粘贴到其中。

```
import time

class Timer(object):
    def __init__(self, verbose=False):
        self.verbose = verbose

    def __enter__(self):
        self.start = time.time()
        return self

    def __exit__(self, *args):
        self.end = time.time()
        self.secs = self.end - self.start
        self.msecs = self.secs * 1000  # millisecs
        if self.verbose:
            print 'elapsed time: %f ms' % self.msecs 
```

Enter fullscreen mode Exit fullscreen mode

现在你所要做的就是，在你想要计时一段代码的模块中，

```
from timer import Timer
...
with Timer() as t:
    some_slow_function()
print 'elapsed: %ss' % t.secs 
```

Enter fullscreen mode Exit fullscreen mode

可以用`t.msecs`以毫秒显示。

这是一个简单而优雅的记录花费时间的解决方案，这类模块可能必须放在一个私有的 python-utils 包中，我可以将它导入到任何我想要的项目中。

[![track](img/65d245f0fa9e54c8ecb1d9cafb89ea07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fXNPSuEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bkit.co/w6id79ay)