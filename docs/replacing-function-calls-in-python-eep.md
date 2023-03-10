# 在 Python 中替换函数调用

> 原文：<https://dev.to/r0f1/replacing-function-calls-in-python-eep>

我最近在 Python 中发现了一个方便的函数，我想和你分享一下。考虑下面这段代码:

```
# This code is not modifiable 
# a.py from scipy.spatial.distance import cdist

def test_function():
    coords = [(0,0), (3,4)]
    y = cdist(coords, coords, metric="euclidean")
    print(y) 
```

Enter fullscreen mode Exit fullscreen mode

该函数使用一个名为`cdist()`的外部[距离函数](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.cdist.html)，并计算一些(硬编码)点列表的欧几里德距离。每对点之间的距离矩阵会打印到控制台。到目前为止，您在代码中使用了这个函数，一切都很好。

```
# Your code = modifiable from a import test_function

test_function()

# Prints
# [[ 0\.  5.]
#  [ 5\.  0.]] 
```

Enter fullscreen mode Exit fullscreen mode

但是，您决定从现在开始不再使用欧几里德距离。你想用其他函数代替`cdist()`。有一个问题:不能修改上面的代码。你的同事写了上面的代码，他不在城里。但是你还是要用这个功能。完全重写也是不可能的，因为你认为函数太复杂了。

`mock.patch()`解决方案:

```
# Your new code from a import test_function
from unittest import mock

def rogue_cdist(x1, x2, metric):
    print("rogue_cdist called")

with mock.patch('a.cdist', wraps=rogue_cdist):
    test_function()

# Prints
# rougue_cdist called
# None 
```

Enter fullscreen mode Exit fullscreen mode

[mock.patch()](https://docs.python.org/3/library/unittest.mock.html#where-to-patch) 工作原理是用另一个名字临时改变一个名字所指向的对象。如上所示，您可以将`mock.patch()`与上下文管理器一起使用。在上面的例子中，您的新的`rogue_cdist()`函数被调用，现在您可以用您喜欢的任何距离度量来填充它。

我认为这是非常聪明和干净的，允许你对现有的代码进行修改，而不需要臭名昭著的复制粘贴式的编码风格。