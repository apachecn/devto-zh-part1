# 用模拟进行 Python 单元测试——第二部分

> 原文：<https://dev.to/mistermocha/python-unit-testing-with-mock---part-two>

# 怎么写测试？

Python 有许多用于编写测试的框架，但最终它们归结为从一个有组织的名称空间中收集名称以`test`开头的函数。这篇文章是“使用模拟进行 Python 单元测试-第一部分”的续篇，并不是为了帮助组织您的名称空间(为了记录起见，[最好用`py.test`](https://docs.pytest.org/en/latest/) 来完成，它值得拥有自己的精彩文章)。

本文将向您展示如何编写测试，并理解何时以及如何使用`mock`模块。仅仅知道“哦，这就是模拟的工作原理”是一回事，但是完全知道如何应用模拟来正确地测试你的代码是另一回事。本文试图解决这方面的知识。

# 我现在实际上是怎么用 mock 的？

拥有所有的工具并不一定意味着知道如何使用它们！当我第一次阅读 mock 的文档时，我发现它令人困惑。我真的不明白怎么把这些联系起来。这里有一些关于如何阅读你的代码和决定如何设计一个测试的实际例子。

## 例 1

让我们来看一个类似于我们在上一篇文章中看到的例子。这里有一个使用 [`requests`库](http://docs.python-requests.org/en/master/)的函数。

```
def get_example():
    r = requests.get('http://example.com/')
    return r.status_code == 200 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中，我们有一个实例化的请求`r`并查看对象的`status_code`属性。`requests`对象是与外界对话的对象，但是我们实现`requests`的代码是我们想要测试的。

我们需要做的是用 mock 替换`requests.get()`的返回。我们将使用 mock 的各种特性来适当地替换它。

```
@mock.patch('requests.get', autospec=True)
def test_get_example_passing(mocked_get):
   mocked_req_obj = mock.Mock()
   mocked_req_obj.status_code = 200
   mocked_get.return_value = mocked_req_obj
   assert(get_example())

   mocked_get.assert_called()
   mocked_get.assert_called_with('http://example.com/') 
```

Enter fullscreen mode Exit fullscreen mode

让我们浏览一下，以展示我们正在做什么以及为什么这样做

*   `mock.patch`使用`autospec=True`给`requests.get`打上模拟补丁(意指`requests`库中的`get`函数)，以匹配`requests.get`的签名。
*   这个被修补的对象作为第一个参数可用，我们在`mocked_get`中接收到它。
*   我们知道我们需要在测试代码中创建一个返回给`r`，所以我们创建了`mocked_request_object`。在代码中，我们只检查了`status_code`，所以我们声明了这个值——在本例中是 200
*   然后，我们将`mocked_request_object`赋给`mocked_get`的`return_value`，这是被修补的函数被调用时将返回的内容
*   因为我们知道函数有效地返回布尔值，我们可以在函数的返回上调用一个简单的`assert`。
*   为了确保 mock 被正确调用，我们调用了内置的断言方法。`assert_called`确保被模仿的`requests.get`被真正调用，`assert_called_with`检查参数。我们也可以对返回的 mock 进行类似的自省。

对于一个小功能(3 行代码)来说，这看起来像是很多测试代码(8 行代码)。通常情况下，python 单元测试可能比它测试的代码拥有更多的 LOC。我们甚至没有完整的测试覆盖，因为 200 不是唯一有效的答案！测试代码应该比产品代码写得更快，因为它不涉及相同层次的思想或设计。

这只是一个展示如何设计一个 mock 来替换接口库的例子。这里的原则是理解你的代码的哪一部分与公众对话，并尽可能少地替换它。

## 例 2

它被称为“模拟”，因为它可以假装是真实的东西。我们知道我们的代码，所以现在我们可以检查我们的代码，以确定如何使用模拟。我们对部件和用法有一个很好的概述，但是实现才是最重要的。

当查看您的代码并决定如何测试时，您希望运行一个测试来确保在执行代码时出现期望的输出和副作用。理想情况下，您希望确保每行代码至少进行一次(如果不是多次)测试。因此，测试应该覆盖每个功能，模拟应该防止这些功能影响外部世界。

下面是一个写数据库的模块的例子:

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

现在这个模块使用了一个名为`DBLibrary`的虚构 API 模块。让我们假设`DBLibrary`是一个已经被信任可以工作的外部库。我们知道这样的库有几个值得注意的特性:

*   这超出了本代码的范围
*   它可能已经有了自己的测试——即使没有，参考前面的观点
*   它可以改变数据库中的状态

这些都是被嘲笑的成熟事物的完美特征。也就是说，我们需要为`DBWriter`编写测试，这是我们的库。让我们看看这个模块的行为。有效的`save`和`drop`动作如下:

*   准备 sql 语句
*   将语句写入数据库
*   递增计数器

所有这些行为都是需要测试的元素，但是我们仍然希望保护数据库不被写入。我们怎么做呢？

### 型号 1 -补丁`commit_to_db`

对数据库的实际写入发生在`commit_to_db`函数中。我们可以用一个 mock 替换这个函数，并复制其行为。它做两件事:

*   递增计数器
*   调用`self.db.commit`提交到数据库

你应该这样写这个测试:

```
@mock.patch('dbwriter.DBWriter.commit_to_db', autospec=True)
def test_save(mock_commit):
    writer = DBWriter()

    def fake_commit(self, sql):
        writer.counter += 1

    mock_commit.side_effect = fake_commit

    writer.save("Hello World")

    mock_commit.assert_called_with(writer,
        "INSERT INTO mytable SET mystring = 'Hello World'")
    assertEquals(writer.counter, 1) 
```

Enter fullscreen mode Exit fullscreen mode

注意，命名空间假设`DBWriter`在文件名`dbwriter.py`或其他这样组织的模块中。您需要知道您的模块的名称空间，这个补丁才能工作。

`@mock.patch`装饰器取代了`commit_to_db`函数。我们声明与签名匹配的`autospec=True`，以确保`commit_to_db`被正确调用。

我们还添加了一个*副作用*，这是除了包含返回值之外发生的事情。不仅仅是写入数据库，它还增加了一个计数器。添加这个行为的副作用是确保在调用 mock 时不会忽略这个动作。因为我们用一个模拟函数替换了这个函数，所以这个函数的实际代码永远不会被调用。

以这种方式调用 mock 给我们带来了以下好处:

*   内省到`commit_to_db`:我们可以调用 mock 的内省工具来确保函数接收到正确的参数
*   与写操作隔离:我们在测试中没有提出与`self.db.commit`等价的操作，这意味着我们已经删除了可能会写入数据库的操作。

然而，这也有一些缺点:

*   陷入`self.db.commit`的迷失自省:我们可能是绝缘的，但是我们不知道我们是否调用了正确的函数。
*   没有实际的`commit_to_db`练习:我们正在重写单元测试中不改变状态的代码！如果这一行更复杂，或者这里有大量代码，我们就必须全部重新实现！

现在让我们尝试另一种方法:

### 模式二:打补丁 self.db.commit

```
@mock.patch('dbwriter.DBLibrary', autospec=True)
def test_save(mock_dblib):
    writer = DBWriter()
    writer.save("Hello World")
    mock_dblib.return_value.commit.assert_called_with(writer,
        "INSERT INTO mytable SET mystring = 'Hello World'") 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们直接在`dbwriter`名称空间中修补`DBLibrary`，这防止了在其他地方修补该库。这是直接手术。

这样做是用一个模仿来代替`DBLibrary`。同样，`autospec=True`将确保对`commit`和任何其他实现的方法的调用将尊重与这些方法的给定签名相匹配的调用。

这是我们通过这种方式测试得到的结果:

*   数据库隔离:和以前一样，只是方式不同
*   反思我们如何使用`DBWriter`模块:注意我们正在调用`assert_called_with`,并确定我们如何调用实际写入数据库的函数，具有相同的保护
*   我们测试中的每一行代码的完整练习:只有外部代码被模拟代码取代

以下是我们失去的:

*   对父函数的内省:我们看不到函数是如何被它在模块中的邻居调用的

幸运的是，这两个测试都是有效的，可以并行进行。如果你不确定写哪个测试，两个都写！你不能有太多的测试覆盖面。

## 例 3

经常有人问我如何模仿在一个对象中被实例化多次的东西。即将出现的类将两次实例化一个模块(例如，两个单独的数据库连接)。仅仅简单地修补基础模块可能会给你一个被使用两次的实例，或者仅仅是抛出对你没有任何帮助的默认模仿。我花了一些研究和实验来找出如何做到这一点。

所以，这里有一些示例代码:

```
from some.library import AnotherThing

class MyClass(object):
    def __init__(self, this, that):
        self.this = AnotherThing(this)
        self.that = AnotherThing(that)

    def do_this(self):
        self.this.do()

    def do_that(self):
        self.that.do()

    def do_more(self):
        got_it = self.this.get_it()
        that_too = self.that.do_it(got_it)
        return that_too 
```

Enter fullscreen mode Exit fullscreen mode

`self.this`和`self.that`都是`AnotherThing`的实例，但分别用`this`和`that`初始化。在整个代码中都会用到它们。我们想要修补`AnotherThing`,这样我们就可以明确地测试我们的代码。

这里我们可以采用两种方法，但有一些注意事项。最终，您选择的选项与您如何实现代码有关。

### 方法#1:直接贴`this`和`that`

如果您知道 MyClass 的`__init__`和`AnotherThing`的初始化没有主动改变状态:
，那么这种方法是有效的

```
def test_my_class():
    my_obj = MyClass("fake this", "fake that")
    my_obj.this = Mock(spec_set='some.library.AnotherThing')
    my_obj.that = Mock(spec_set='some.library.AnotherThing')

    my_obj.do_this()
    my_obj.this.do.assert_called()
    my_obj.do_that()
    my_obj.that.do.assert_called() 
```

Enter fullscreen mode Exit fullscreen mode

在这种方法中，我们实例化然后就地修补结果。这是一个简单的方法，只有当你知道你的代码符合这个模式时，它才真正有效。在`__init__`代码中发生状态改变的可能性极小，但是完全有可能。实例化`AnotherThing`也更有可能是缓慢且昂贵的，即使它没有改变任何外部状态。所以这个可能不那么混乱，不一定更好。

### 方法#2:模拟生成器

`AnotherThing`返回一个`AnotherThing`的实例，那么为什么不直接修改这个动作来模仿呢？

```
@patch('yourcode.AnotherThing', autospec=True)
def test_my_class(mock_thing):
    def fake_init(*args):
        return Mock(*args, spec_set='some.library.AnotherThing')
    mock_thing.side_effect = fake_init

    my_obj = MyClass("fake this", "fake that")
    my_obj.this.called_with("fake this")
    my_obj.that.called_with("fake that") 
```

Enter fullscreen mode Exit fullscreen mode

`mock_thing`是要替换`AnotherThing`的命名空间内补丁。我们利用使用`side_effect`来返回一个`spec_set`模拟。现在我们已经用`mock.patch`给`AnotherThing`打了补丁，让它吐出看起来像它创造的物体的模仿物。我所有的修补都发生在实例化之前，所以这里没有修改被测试的代码对象。

此外，模拟允许自省，因此我们也可以测试使用情况！

# 嘲笑还是不嘲笑？

有时候模仿是必要的，也是合适的，我们已经介绍了一些例子，说明了在单元测试的范围内，模仿是绝对必要的:

*   调用 API
*   连接到数据库
*   使用命令行

这些都是值得考虑的明显例子。让我们看看一些不太明显的，也是不要嘲笑的理由

*   *不要嘲笑文件系统。*在[摩西·扎德卡的博客](https://moshez.wordpress.com/2016/12/02/dont-mock-the-unix-filesystem/)中可以找到一个很好的解释。文件系统非常大。非常大。试图模仿它将是一个巨大的可怕的模仿，有许多边缘需要替换。如果你需要一个文件句柄，使用`StringIO`创建一个假的，或者使用`tempfile`写入一个临时文件。两者都是标准库，易于实现。
*   *验收测试*是我们如何确保我们的代码与其余的企业导入和第三方库一起工作。不要忽视这些库可能会以意想不到的方式改变。编写您的单元测试，但是为偶尔的运行编写一些验收测试
*   根据定义，集成测试是为了测试你的代码如何与环境的其他部分集成。在某些时候，您可能希望偶尔测试一下真正的 API 调用。
*   模仿缓慢的库来加速你的单元测试。一些代码实例化的成本很高，但实际上并不需要完全拿出来测试。把这个抽象掉。
*   如果你在写测试，你就做对了！