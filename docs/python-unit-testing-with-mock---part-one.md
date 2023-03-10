# 用模拟进行 Python 单元测试——第一部分

> 原文：<https://dev.to/mistermocha/python-unit-testing-with-mock---part-one>

# 这是什么文章？

几个月前，我被邀请到普吉特湾 Python 聚会小组做一次演讲。这是这篇演讲的文章翻译。这篇演讲做了一些假设，假设你知道如何实际编写 python 代码测试的基本知识，并且需要知道如何嵌入模拟来保证代码的安全。

当我被邀请做这个演讲时，我(现在仍然)负责一个大的脚本，这个脚本与大量的外部服务进行对话:

*   网络认证协议（名字来源于希腊神话中“三个头的狗——地狱之门守护者”）
*   饭桶
*   曙光
*   一揽子回购
*   吉拉
*   电子邮件
*   共享库

这就提出了一个问题，如何在不接触这些服务的情况下测试我的代码？

注意这篇文章是模拟测试库的专有文章。构建全面的测试是另一个主题，应该有自己的文章，我现在还没有写。当我到那里的时候，我会让你知道！

# 什么是测试？

测试通过**运行你的代码**并观察结果来确保你的代码按预期运行。通过实际运行程序块代码，这是知道它在运行、如何运行以及它做了预期的事情的最可靠的方法。

当编写测试时，您可以归类到三个近似的类别中的一个。

*   单元:就这一小部分
*   集成:当所有的部分都互相交流时，包括部分
*   接受:当整个应用程序与其他所有东西对话时

本文将关注模拟的最佳用途，那就是编写单元测试。模拟可能在集成测试中占有一席之地，而应用程序不会改变。

一些代码很容易运行，以确保它做了预期的事情。这里有一个快速的函数和单元测试，你可能会看到:

```
### code ### def double_up(number):
    return number * 2

### test ### def test_double_up():
    assertEquals(double_up(2), 4) 
```

Enter fullscreen mode Exit fullscreen mode

单元测试只是运行代码，并将接收到的输出与预期的输出进行比较。正如前面所说的，**测试意味着运行你的代码**

现在找点乐子...

## 你如何测试这个

在不删除任何内容的情况下，如何测试这段代码？

```
def wipe_directory(path):
    p = Popen(['rm', '-rf', path], stdout=PIPE, stderr=PIPE)
    if p.wait():
        raise Exception('We had a fail') 
```

Enter fullscreen mode Exit fullscreen mode

在不删除任何内容的情况下，如何测试这段代码？

```
def delete_everything():
    r = requests.post('http://example.com/',
        data={'delete': 'everything', 'autocommit': 'true'})
    if r.status_code == 200:
        print('All things have been deleted')
        return True
    else:
        print('Got an error: {}'.format(r.headers))
        return False 
```

Enter fullscreen mode Exit fullscreen mode

如何在不对数据库进行任何更改的情况下测试这段代码？

```
class DBWriter(object):
    counter = 0

    def __init__(self):
        self.db = DBLibrary()

    def commit_to_db(self, sql):
        self.counter += 1
        self.db.commit(sql)

    def save(self, string):
        sql = "INSERT INTO mytable SET mystring = '{}'".format(string)
        self.commit_to_db(sql)

    def drop(self, string):
        sql = "DELETE FROM mytable WHERE mystring = '{}'".format(string)
        self.commit_to_db(sql) 
```

Enter fullscreen mode Exit fullscreen mode

尝试实际执行这些代码块将会修改外部状态。这些例子要么执行一个 shell 命令，与一个 API 对话，要么连接到一个数据库，所有这些都是发生在所编写代码的执行空间之外的动作。

# 什么是嘲讽？

`unittest.mock`是一个用于 Python 测试的库。它允许您用模拟对象替换测试中的系统部分，并断言它们是如何被使用的。

来源:https://docs.python.org/3/library/unittest.mock.html

如前所述，模拟主要用于单元测试。在集成测试中可能会有一席之地，但在验收测试中不太可能。

为什么要在代码中嘲笑呢？

*   安全地进行单元测试:停止状态改变部分，这样您就可以实际运行测试了
*   编写更好的代码:编写全面测试的一个可爱的副作用
*   隔离:为了更安全的测试，在你的代码和“非你的代码”之间建立墙

## 关于模拟图书馆

模拟对象旨在替换代码的另一部分，这样它就假装是那个代码。在这个例子中，让我们假设`visible_method`是运行`sub_method`的`MyClass`内部的函数。现在让我们假设`sub_method`做了一些我们不想在这个测试范围内运行的事情。我们简单地用一个`Mock`实例替换`sub_method`。

```
from unittest.mock import Mock
from mycode import MyClass

def test_myclass():
   my_object = MyClass()
   my_object.sub_method = Mock()
   my_object.visible_method()
   my_object.sub_method.assert_called_with("arg", this="that") 
```

Enter fullscreen mode Exit fullscreen mode

这将一个函数与同一个类中的另一个函数隔离开来。一个 mock 变成了一个合成的代码对象，每次都成功地运行并返回另一个 Mock 对象(有一些例外)。除非您期望一些特定的东西，否则原始的模拟对象几乎可以出现在任何地方。

然而，大多数函数都期望一些特定的东西，这就是 mock 的主要目的。你可以把模型做成任何样子。支持它像一个特定的函数、模块、类、对象等一样工作。您将避免做一些冒险的事情，比如调用数据库 API。我们将在接下来的一些例子中得到更多的信息。

## 使用 mock.patch

有时候，放入一个对象并不像直接替换名称空间中的一个函数那么容易。有时要替换的对象要深得多。通过在函数中添加`@mock.patch` decorator，您可以用 mock 直接替换名称空间中的对象。

这里有一个名字空间中有`subprocess.Popen`的函数。我们不能像前面的例子那样直接替换它。

```
def count_the_shells():
    p = Popen(['ps', '-a'], stdout=PIPE, stderr=PIPE)
    if p.wait():
        raise Exception('We had a fail')
    count = 0
    for proc in p.stdout.readlines():
        if "-bash" in proc:
            count += 1
    return count 
```

Enter fullscreen mode Exit fullscreen mode

`subprocess.Popen`在函数内部，但它在函数的名称空间中可用。通过模仿，我们用模仿代替了 T2。由于替换，不再使用`PIPE`了，所以我们不需要修补它们。

通过检查这个函数中的动作，我们看到它的表面与返回的`subprocess`对象对话:

*   `Popen`运行命令行执行并返回子进程对象。在这种情况下，`p`
*   `p.wait()`阻塞，直到它获得 shell 的退出代码，并以整数形式返回。
*   `p.stdout`是一个捕获标准输出的类似文件的对象

我们的模拟也需要这些表面。我们不需要完全替换一个`Popen`调用的行为，我们只需要让返回值*看起来像*一个`Popen`调用的返回值和我们使用的方法。

```
@mock.patch('subprocess.Popen')
def test_count_the_shells(mocked_popen):
    mocked_popen.return_value.stdout = open('testps.out')
    mocked_popen.return_value.wait.return_value = False
    assert count_the_shells() == 4 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这个测试中发生了什么:

*   `@mock.patch` decorator 用一个模拟对象替换了`subprocess.Popen`。作为测试函数的第一个参数传入。测试函数接收它作为`mocked_popen`
*   `Popen`调用返回一个子流程对象。我们现在通过将行为应用到在函数中使用的`stdout`和`wait`来修改该对象的`return_value`
*   现在当执行`count_the_shells`时，它调用 mock 而不是`Popen`并获得预期值。

就函数而言，我们已经用一个行为类似于该对象的 mock 有效地替换了`subprocess.Popen`。现在您知道了如何用一个模拟来替换一个方法，这个模拟实际上模拟了一些东西！

但是等等，还有呢！

## 规格和自动规格

如果您的模拟可以指向一个模块，并自动看起来像那个模块，会怎么样？如果它可以被提示只对模块中已经定义的函数做出反应，会怎么样？看起来就像是我们期待和喜爱的 python-majick，对吗？好吧，继续读下去！！

一个 mock 有自己的内置函数，但是收到的任何其他调用都会简单地返回另一个 mock。这种塑性行为看起来是这样的:

```
>>> mock = Mock()
>>> mock.this_is_never_assigned('hello')
<Mock name='mock.this_is_never_assigned()' id='4422797328'> 
```

Enter fullscreen mode Exit fullscreen mode

这可以防止意外调用破坏您的代码，但是也为许多错误留下了空间。您可以通过自动检查来获得更安全的实例化——使模仿的行为更像您正在模仿的东西。传递参数`spec`告诉 mock 要表现得像另一个。用`spec=RealObject`实例化的模拟将通过`isinstance(the_mock, RealObject)`。

```
>>> from collections import OrderedDict
>>> mymock = Mock(spec=OrderedDict)
>>> isinstance(mymock, OrderedDict)
True
>>> type(mymock)
<class 'mock.Mock'> 
```

Enter fullscreen mode Exit fullscreen mode

使用`spec`也提供了保护，防止调用未声明的属性。您可以根据需要声明任何附加属性。

```
>>> mymock = mock(spec=OrderedDict)
>>> a = mymock.this_does_not_exist()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/twitter/lib/python2.7/site-packages/mock.py", line 658, in __getattr__
    raise AttributeError("Mock object has no attribute %r" % name)
AttributeError: Mock object has no attribute 'this_does_not_exist'

>>> mymock.this_does_not_exist = "this exists now"
>>> print(mymock.this_does_not_exist)
this exists now 
```

Enter fullscreen mode Exit fullscreen mode

用`spec_set`实例化是一个更加*严格的*规范。这可以防止修改丢失的属性。尝试定义未声明的属性将在`AttributeError`
失败

```
>>> mymock = Mock(spec_set=OrderedDict)
>>> mymock.this_does_not_exist = "o no you didn't"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/twitter/lib/python2.7/site-packages/mock.py", line 761, in __setattr__
    raise AttributeError("Mock object has no attribute '%s'" % name)
AttributeError: Mock object has no attribute 'this_does_not_exist' 
```

Enter fullscreen mode Exit fullscreen mode

使用`create_autospec`函数就更严格了。这将生成定义为 spec 的模拟函数，它将强制函数签名，这意味着如果原始函数需要两个位置参数和一个关键字参数，那么模拟函数也需要两个位置参数和一个关键字参数。

```
 >>> def myfunc(foo, bar):
...     pass
...
>>> mymock = create_autospec(myfunc)
>>> mymock("one", "two")
<MagicMock name='mock()' id='4493382480'>
>>> mymock("just one")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 2, in myfunc
TypeError: <lambda>() takes exactly 2 arguments (1 given)
>>> 
```

Enter fullscreen mode Exit fullscreen mode

适当地使用 spec 可以帮助您编写更干净的代码，并发现错别字。由于 spec_set 锁定了未实现的方法，如果您的测试代码也有输入错误，那么它将会失败。

```
>>> mock = Mock(name='Thing', return_value=None)
>>> mock(1, 2, 3)
>>> mock.assret_called_once_with(4, 5, 6)
# typo of "assert" passes because mock objects are forgiving 
>>> from urllib import request
>>> mock = Mock(spec=request.Request)
>>> mock.assret_called_with
Traceback (most recent call last):
...
AttributeError: Mock object has no attribute 'assret_called_with'
# since "assret_called_with" is a typo, it's not declared. Proper exception caught! 
```

Enter fullscreen mode Exit fullscreen mode

同样，在 python 3 中，你可以`name`你的模仿，这显示在模仿的 repr 中。这对调试很有用！您可以确切地看到您的模拟在代码中的什么地方出了问题，因为您知道您的哪个模拟被误用了！

## 自省

mock 附带了一长串内置函数，这些函数提供了对 mock 生命周期的一定程度的内省。有一些存储属性可以自动存储 mock 中的每个函数调用。这些是用于检查如何使用模拟的内置属性和函数:

*   `called` -布尔值，如果调用过，则为真
*   `call_count` -整数，调用次数
*   `call_args` -带有上次调用参数的 mock.call()对象
*   `call_args_list` -包含曾经使用过的所有参数的 mock.call()列表
*   跟踪对方法和属性的调用，以及它们的派生
*   `mock_calls` -对模拟对象的所有调用的*列表*

如果您有一个模拟对象通过您的模块，并且您想确保它确实被引用并且没有被遗漏，那么`called`方法是您的好朋友。同样，`call_count`可以确认使用了多少次，`call_args`可以确认最后一次使用了哪些参数。

既然这是针对单元测试的，断言是编写测试的常用方法。为了更好地实现这一点，mock 内置了引用上述属性的断言函数:

*   如果有电话的话
*   `assert_called_once` -如果正好被调用一次
*   `assert_called_with` -上次调用中使用的特定参数
*   `assert_called_once_with` -特定参数只使用一次
*   `assert_any_call` -任何调用中使用的特定参数
*   `assert_has_calls` -类似于“任何呼叫”,但有多个呼叫
*   `assert_not_called` -从未被召唤过

因此，您不必在 if 语句中包装布尔值，您可以只使用内置的。

# 总结——第一部分

在本文中，我们介绍了 python 中的`mock`模块的用法和特性。我们讨论了如何将 mock 应用到现有的测试中，以及如何调整它的行为。

在 [Python 单元测试与模拟第二部分](https://dev.to/mistermocha/python-unit-testing-with-mock---part-two)中，我们将介绍如何审查您的代码以决定您的代码的哪些部分需要模拟，如何用真实的代码样本编写它们，以及一些您可能想要模拟但不应该模拟的情况。