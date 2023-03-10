# 关于使用 Python3 进行单元测试和模拟的全面介绍

> 原文：<https://dev.to/perigk/a-comprehensive-introduction-to-unit-testing-and-mocking-with-python3-624>

单元测试正迅速成为所有招聘广告的必备条件。有一些人不喜欢测试驱动开发(TDD ),但至少他们认同自动化测试(不仅仅是单元测试)对整体代码质量和调试过程的价值。

根据 dice.com 的说法，在撰写本文时，TDD 正一年比一年受欢迎。

事不宜迟，让我们开始我们的旅程。你可以从[这里](https://github.com/PeriGK/filesystem_handling/)下载源代码

#### 一个关于单元测试的小背景

假设我们有一个新的功能，它实现了一个购物车，我们想要测试它。我们可以使用两大类测试方法。自动化的和非自动化的。

如果我们选择非自动化的方式，我们可以使用鼠标或键盘将一些产品添加到购物车中，并“作为用户”验证功能。在很长一段时间里，这是测试软件最流行的方法。但是随着新的令人兴奋的测试框架被创建，人们开始将注意力转移到自动化测试方法上。

自动化方式要求开发人员编写一些字节的代码，测试(业务)逻辑。给定购物车，在前面的段落中，我们将使用代码使其自动发生，而不是点击这里和那里。这就像是由机器而不是人类执行的测试场景。

#### 变脏(带代码)

在上面的示例代码中，我编写了一个小工具，它在文件系统上执行一些基本操作。让我们深入源代码和相关的测试用例，以便更好地理解单元测试。

```
import os
def create_file(target_directory, new_file_name):
 if check_dir_exists(target_directory) and not check_file_exists(new_file_name): 
    new_file_name = os.path.join(target_directory, new_file_name)
    with open(new_file_name, ‘w’) as f:
    f.write(‘’)
    return True
 return False 
```

在上面的函数中，我们想要创建一个新文件。在这个函数中，我们分两部分提供目标文件的完整路径，即它所在的目录和它的文件名。我们检查目录是否在那里，但是我们不希望文件已经存在。如果我们满足这两个条件，我们就创建文件。

在进入单元测试代码之前，对于这个函数，让我们解释一下什么是嘲讽。

#### 嘲讽

*检查目录存在*和*检查文件存在*是如何工作的？这就是单元测试的好处。你不需要知道。你所需要知道的是，它是如何被调用的，返回类型是什么，为了强制期望的返回值。这个过程叫做嘲讽。因此，您不用实际调用方法/对象，而是使用一个虚拟的方法/对象，可以根据您的需要进行定制。

我喜欢把嘲讽看作是一种不去理会“调用外部代码”的艺术。

> 我的代码依赖于一个外部服务，因此我不能对它进行单元测试。

根据您的测试场景，模拟外部调用并返回预期值。如果调用应该将一些数据放入数据库，那么根据 API 响应，返回一个 JSON/XML/whatever 响应，表示成功。在这里，相应的 API 文档是您最好的朋友。

> 您让我进行单元测试的功能是删除磁盘中的整个分区。

那也行(差不多)。您可以模拟系统调用，并验证调用的顺序是否正确。

> 我无法测试这段代码，因为它使用了内置函数和第三方函数。

是的，你猜对了。模仿前面提到的相关函数。

如您所见，您对代码的依赖越少，单元测试就越容易编写。不幸的是，这说起来容易做起来难。

在我们的例子中，我们将使用 unittest 库的 mock 模块来实现这样的效果。

#### 如何用 python 写单元测试——样板部分。

python 中的单元测试是一个函数，位于从 *unittest 继承的类中。测试用例*。你可能听说过那些被称为*测试套件*的测试，但是出于本文的考虑，我们将称它们为*测试用例*。每个*测试用例*都有一些标准的助手方法，它们贯穿单元测试过程。

下面你可以看到两个最常见的例子。*安装*和*拆卸*。正如注释中所解释的那样(也许从它们的名字就可以一目了然) *setUp* 正在初始化单元测试所需的值，并且在测试执行之前运行(你好，队长很明显)。 *tearDown* 做的正好相反，它在单元测试执行之后清理东西并运行。

```
import unittest
from unittest import mock
from fs_handler_main import *

class FileSystemHandlerTest(unittest.TestCase):
 def setUp(self):
 “””setUp is run before each test case. 
 It is usually used to setup(wow) some common
 values for all test cases”””
     print(‘setUp: Initialize instance variables’)
     self.directory = ‘/home/periklis/’
     self.new_filename = ‘dummy_new_filename.txt’

# Fun fact: The os module we are using is 
 # indirectly imported from fs_handler_main and not
 # from the python library
     self.full_path = os.path.join(self.directory, 
     self.new_filename)
 def tearDown(self):
 # This is something not very useful, it is only written
 # this way to demonstrate how tearDown works
     print(‘tearDown: Reset instance variables’)
     self.directory = ‘’
     self.new_filename = ‘’ 
```

#### 如何用 python 写单元测试——单元测试业务逻辑。

出于本文的目的，我为上面看到的 *create_file* 函数编写了三个单元测试。记住，你可以写尽可能多的测试场景，并找到有用的。极限中的天空或者如巴兹所说，[到无限单元测试以及超越](https://www.youtube.com/watch?v=ejwrxGs_Y_I)

```
@mock.patch(‘builtins.open’, mock=mock.mock_open)
 @mock.patch(‘fs_handler_main.check_file_exists’)
 @mock.patch(‘fs_handler_main.check_dir_exists’)
 def test_create_file_success(self, mock_check_dir_exists, 
 mock_check_file_exists, mock_open_func):
     print(‘test_create_file_success’)
     mock_check_dir_exists.return_value = True
     mock_check_file_exists.return_value = False
     self.assertTrue(create_file(self.directory, self.new_filename))

@mock.patch(‘builtins.open’, mock=mock.mock_open)
 @mock.patch(‘fs_handler_main.check_file_exists’)
 @mock.patch(‘fs_handler_main.check_dir_exists’)
 def test_create_file_failure_dir_no_exists(self, mock_check_dir_exists, mock_check_file_exists, mock_open_func):
     print(‘test_create_file_failure_dir_no_exists’)
     mock_check_dir_exists.return_value = False
     mock_check_file_exists.return_value = False
     self.assertFalse(create_file(self.directory,self.new_filename))

@mock.patch(‘builtins.open’, mock=mock.mock_open)
 @mock.patch(‘fs_handler_main.check_file_exists’)
 @mock.patch(‘fs_handler_main.check_dir_exists’)
 def test_create_file_failure_file_already_exists(self, mock_check_dir_exists, mock_check_file_exists, mock_open_func):
     print(‘test_create_file_failure_file_already_exists’)
     mock_check_dir_exists.return_value = False
     mock_check_file_exists.return_value = False
     self.assertFalse(create_file(self.directory,self.new_filename)) 
```

你有没有注意到测试函数是从 *test_* 开始的？这是因为 python 应用了一个正则表达式，以便在一个单元测试文件中将实际的测试用例与辅助函数分开。

关于上面的测试:

*第一个单元测试是检查正常情况，在这里文件被顺利创建。因此，业务逻辑函数返回 *True* ，即我们断言的值。

*第二个试图创建新文件，但是目录不在那里。因此，通过检查 create_file 的返回值是否为 False，它断言不会创建该文件。

*类似地，第三个单元测试是检查文件是否已经存在，在这种情况下，它不会创建它。

你是不是在想为什么*不使用从 *@mock.patch('builtins.open '，mock=mock.mock_open)* 创建的 mock 对象*？我们只模拟了这一部分，因此在每次测试运行中不会创建实际的文件。我们稍后将讨论更多内容。

**装饰者**

但是每个函数声明上面的这个 *@* 是什么东西呢？我即将为装饰者写一篇文章，因为它们不能用几行文字简单解释，但是现在，你只需要知道，它们是函数，它们将其他函数作为参数，并返回那些扩展的参数。

所以具体来说，[*@嘲弄*](http://twitter.com/mock) *。 *test_create_file_success* 之上的 patch(fs _ handler _ main . check _ file _ exists)*表示:

> 从 fs_handler_main 中找到函数 check_file_exists，只要我们在下面函数(即 fs_handler_main)的范围内，每当调用 check_file_exists 时，就使用一个名为 mock_check_file_exists 的模拟对象。

这对 python 模块也是一样的。例如，如果你想模仿 *os* 或 *math* 你需要提供一个完整的路径，从被测代码模块开始(在我们的例子中是 *fs_handler_main*

#### 摩尔嘲讽者和装饰者

让我们更深入地研究 decorators 和 mocking for unit testing，并检查下面的一对业务逻辑——单元测试代码。

```
def delete_file(full_path_to_file):
    if os.path.isfile(full_path_to_file):
    os.remove(full_path_to_file)

# We mock the fs_handler_main.os.* modules, if we mock the os provided from python directly, it will not work
 @mock.patch(‘fs_handler_main.os.path.isfile’)
 @mock.patch(‘fs_handler_main.os.remove’)
 def test_delete_file_success(self, mock_os_remove, mock_os_is_file):
     print(‘test_delete_file_success’)
     mock_os_is_file.return_value = True
     delete_file(self.full_path)
     mock_os_remove.assert_called_with(self.full_path) 
```

在核心逻辑函数中，我们检查给定的完整路径是否实际上是一个文件，在这种情况下，我们删除它。

当我们使用 python 的操作系统模块时，我们也需要模拟它，这被认为是最佳行业实践。* *不是说不然不行。**但是有太多不必要的事情要处理，在这种情况下，即:

*确保您有权限读/写作为参数提供的目录。

*确保你已经在之前的代码中创建了目标文件(可能是一个帮助函数)。

*确保磁盘有足够的写入空间，即使其他所有先决条件都满足。

如上所述，我们需要模拟 fs_handler_main 文件提供的操作系统模块，而不是原始的模块(功能是相同的，**只是 python 如何处理命名空间**的问题)。

这里重要的是行:

```
mock_os_is_file.return_value = False 
```

我们的业务逻辑依赖于 *os.is_file* 的返回值。因此，首先，我们模拟它，然后根据我们场景的需要，通过改变返回值来定制它。

#### 值得记住的事情

感谢您阅读这篇文章。下面你可以找到我认为值得重申的几点。

*作为测试场景的方法从 *test_* 开始，以帮助 python 轻松发现它们。

*依赖项越少，单元测试就越容易编写。

*使用被测模块(业务逻辑)名称空间模拟事物，而不是 python 名称空间。

#### 进一步阅读

* [Python 文档](https://docs.python.org/3/library/unittest.html?highlight=unittest#module-unittest)

* [测试 Python:应用单元测试、TDD、BDD 和验收测试](https://www.amazon.co.uk/gp/product/1118901223/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=1118901223&linkCode=as2&tag=bitperi-21&linkId=e6220e748c81612f7f772a32d236e5b9)

* [Nose 框架](http://nose.readthedocs.io/en/latest/)