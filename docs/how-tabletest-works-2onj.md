# Tabletest 的工作原理

> 原文：<https://dev.to/horia141/how-tabletest-works-2onj>

在[以前的](///tabletests.html)帖子中，我介绍了`tabletest`库。在这篇文章中，我将深入探讨一下它的内部工作原理。然而，水相对较浅，因为注释代码不到`80`行，库一开始就没有那么大。

我开始这个项目是为了让为 [SDHash](https://github.com/horia141/sdhash) 项目编写测试更加容易。因此，我对自己想要的 API 有一个非常清晰的想法。我不知道 Python 中有哪些可用的工具，也不知道我实际上应该如何写这个东西。事实上，我甚至不知道这样的事情是否可行。正如我们将看到的，这确实是可行的(剧透！)，通过使用 Python 元类，这是该语言中更强大的特性之一。

我希望 API 非常简单——写一个测试用例的列表/集合/字典/任何可迭代的东西，并用测试用例注释一个“种子”函数。这个库应该解决剩下的问题，施展它的魔法，为每个测试用例生成一个测试函数，就像在[之前的](///tabletests.html)文章中描述的那样。

用测试用例标记“种子”函数是通过`tabletest`注释完成的。它接收一个参数，这个参数是测试用例的一个可迭代对象，每个测试函数都是从这个对象中生成的。作为一个实现细节，注释只是将测试用例作为一个字段添加到它所应用的函数对象中。该功能也被标记为正在被`tabletest`处理..

看一下代码:

```
class tabletest(object):
    def __init__ (self, test_cases):
        self._test_cases = iter(test_cases)

    def __call__ (self, tester_fn):
        tester_fn._is_tabletest = True
        tester_fn._test_cases = self._test_cases
        return tester_fn 
```

Enter fullscreen mode Exit fullscreen mode

正如你在第一篇[帖子](///tabletests.html)中看到的，这并不是所有需要做的事情。还有一小步。测试套件类必须从`TableTestCase`派生，而不是从`TestCase`派生。前者的基类仍然是`TestCase`，它自己的行为非常简单，因此它应该像其他`TestCase`派生类一样。它唯一做的事情是添加`TableTestMetaclass`作为元类。

真正的奇迹发生在元类中，它可以修改测试套件，就像注释可以修改函数对象一样。它的工作方式很简单。在`__new__`方法中，我们检查对象的属性。目标是构建一个新的属性集，它与原来的属性集相同，但是对于每个测试用例都有一个测试函数。我们寻找那些都是可调用/函数的属性，它们似乎已经被`tabletest`注释修改了。对于这些，我们查看所有指定的测试用例，这仅仅意味着查看其中的`_test_cases`字段，并为每个测试用例生成一个新的测试函数。如果注释的函数是`test_simple`，那么，对于三个测试用例，生成函数`test_simple_0`、`test_simple_1`和`test_simple_2`。每一个都符合`unittest`约定，所以它们是零参数方法。所有其他属性保持不变。

有一些细节需要考虑。首先，按照`unittest`约定，带注释函数的名称必须以`test`开头。第二，可以有不止一个带注释的函数。该库将做“正确的事情”并生成几组测试。第三，如果在生成测试函数时发生名称冲突，就会产生一个错误。第四，也是最后一点，从`TableTestCase`派生不是必要的，而是封装整个元类的好方法。如果由于某种原因，您不能从它派生，您也可以自己指定元类。这就是为什么`TableTestMetaclass`是公有的。

完整代码如下:

```
class TableTestMetaclass(type):
    def __new__ (cls, name, bases, attrs):
        new_attrs = {}
        for name, attr in attrs.items():
            if hasattr(attr, ' __call__') and hasattr(attr, '_is_tabletest'):
                test_idx = 0
                for test_case in attr._test_cases:
                    test_name = '{0}_{1}'.format(name, test_idx)
                    if test_name in new_attrs:
                        raise Exception('Name "{0}" is already used'.format(test_name))
                    new_attrs[test_name] = \
                        lambda self, attr=attr, test_case=test_case: attr(self, test_case)
                    test_idx += 1
            else:
                new_attrs[name] = attr

        return type. __new__ (cls, name, bases, new_attrs) 
```

Enter fullscreen mode Exit fullscreen mode

开始这个项目时，我不知道它是否可行，也不知道它是否符合我的设想。尽管我用 Python 编程已经有一段时间了，我还是设法避免了元类。我只知道在测试工具实例化对象之前，我需要改变或预处理整个类定义的东西。事实证明，元类只是这项工作的工具。

最后要说明的是，之所以有两个包，Python 2 的 [tabletest](https://github.com/horia141/tabletest) 和 Python 3 的 [tabletest3](https://github.com/horia141/tabletest3) ，是因为这两个包指定元类的方式不兼容。这就是生活。

不管是谁，现在就这样。