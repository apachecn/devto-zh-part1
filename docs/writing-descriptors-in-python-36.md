# 用 Python 3.6+编写描述符

> 原文：<https://dev.to/dawranliou/writing-descriptors-in-python-36>

[![alt intercept](img/54e4909956402695250b69219e30ada1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T6AG8v5Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/e/e3/Fresno_State_Texas_Aggies_Blocked_Kick.jpg) 
*美式足球运动员“阻挡”踢球，而不是“拦截”*

*特别感谢[卢西亚诺·拉马尔霍](https://twitter.com/ramalhoorg)。我从他在 PyBay 2017* 的工作坊中学到了大部分关于描述符的知识

你见过这段代码或者可能写过这样的代码吗？

```
from sqlalchemy import Column, Integer, String
class User(Base):
    id = Column(Integer, primary_key=True)
    name = Column(String) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码片段部分来自一个流行的 ORM 包的教程，叫做 [SQLAlchemy](http://docs.sqlalchemy.org/en/latest/orm/tutorial.html#declare-a-mapping) 。如果你想知道为什么属性`id`和`name`没有像[普通类](https://docs.python.org/3/tutorial/classes.html#class-objects)那样被传入`__init__`方法并绑定到实例，这篇文章就是为你准备的。

这篇文章从解释描述符开始，为什么使用它们，如何在以前的 Python 版本中编写它们(<= 3.5,) and finally writing them in Python 3.6 with the new feature described in [PEP 487 -更简单的类创建定制](https://www.python.org/dev/peps/pep-0487/)

如果你很着急，或者你只是想知道有什么新的东西，请一直滚动到这篇文章的底部。你会找到完整的代码。

# 什么是描述符

Raymond Hettinger 在 [Descriptor HowTo Guide](https://docs.python.org/3.6/howto/descriptor.html#definition-and-introduction) 中解释了描述符的伟大定义:

> 通常，描述符是具有“绑定行为”的对象属性，其属性访问被描述符协议中的方法覆盖。这些方法是 __get__()、__set__()和 __delete__()。如果这些方法中的任何一个是为一个对象定义的，那么它就是一个描述符。

有三种方法可以访问属性。假设我们在对象`obj`上有`a`属性:

1.  要查找其值，`some_variable = obj.a`，
2.  要更改其值，`obj.a = 'new value'`，或
3.  要删除它，`del obj.a`

Python 是动态和灵活的，允许用户截取上述表达式/语句，并将行为绑定到它们。

# 为什么要使用描述符

我们来看一个例子:

```
class Order:
    def __init__(self, name, price, quantity):
        self.name = name
        self.price = price
        self.quantity = quantity

    def total(self):
        return self.price * self.quantity

apple_order = Order('apple', 1, 10)
apple_order.total()
# 10 
```

Enter fullscreen mode Exit fullscreen mode

尽管缺乏适当的文档，但还是有一个 bug:

```
apple_order.quantity = -10
apple_order.total
# -10, too good of a deal! 
```

Enter fullscreen mode Exit fullscreen mode

与其使用 getter 和 setter 方法并破坏 API，不如使用 property 来强制`quantity`为正数:

```
class Order:
    def __init__(self, name, price, quantity):
        self._name = name
        self.price = price
        self._quantity = quantity  # (1) 
    @property
    def quantity(self):
        return self._quantity

    @quantity.setter
    def quantity(self, value):
        if value < 0:
            raise ValueError('Cannot be negative.')
        self._quantity = value  # (2)
    ...

apple_order.quantity = -10
# ValueError: Cannot be negative 
```

Enter fullscreen mode Exit fullscreen mode

我们将`quantity`从一个简单的属性转换成一个非负属性。注意行`(1)`的属性被重命名为`_quantity`，以避免行`(2)`获得`RecursionError`。

完事了吗？当然不是。我们忘记了`price`属性也不能是负数。它可能只是试图为`price`创建另一个属性，但是请记住 DRY 原则:当您发现自己两次做同样的事情时，这是提取可重用代码的好迹象。同样，在我们的例子中，将来可能会有更多的属性需要添加到这个类中。对于作者或读者来说，重复代码并不有趣。让我们看看如何使用描述符来帮助我们。

# 如何写描述符

有了描述符，我们新的类定义将变成:

```
class Order:
    price = NonNegative('price')  # (3)
    quantity = NonNegative('quantity')

    def __init__(self, name, price, quantity):
        self._name = name
        self.price = price
        self.quantity = quantity

    def total(self):
        return self.price * self.quantity

apple_order = Order('apple', 1, 10)
apple_order.total()
# 10 apple_order.price = -10
# ValueError: Cannot be negative apple_order.quantity = -10
# ValueError: Cannot be negative 
```

Enter fullscreen mode Exit fullscreen mode

注意在`__init__`方法之前定义的类属性？这很像本文开头展示的 SQLAlchemy 示例。这就是我们要去的地方。我们需要定义`NonNegative`类并实现描述符协议。下面是方法:

```
class NonNegative:
    def __init__(self, name):
        self.name = name  # (4)
    def __get__(self, instance, owner):
        return instance.__dict__[self.name]  # (5)
    def __set__(self, instance, value):
        if value < 0:
            raise ValueError('Cannot be negative.')
        instance.__dict__[self.name] = value  # (6) 
```

Enter fullscreen mode Exit fullscreen mode

第`(4)`行:需要`name`属性，因为当在第`(3)`行创建`NonNegative`对象时，名为`price`的属性的赋值还没有发生。因此，我们需要显式地将名称`price`传递给对象的初始化器，以用作实例的`__dict__`的键。

稍后，我们将看到在 Python 3.6+中我们如何避免冗余。

在 Python 的早期版本中，冗余是可以避免的，但我认为这需要花费太多的精力来解释，这也不是这篇文章的目的。因此，不包括在内。

第`(5)`和`(6)`行:我们需要直接进入`__dict__`对象，而不是使用内置函数`getattr`和`setattr`，因为内置函数也会被描述符协议拦截并导致`RecursionError`。

# 欢迎来到 Python 3.6+版

我们仍然在第`(3)`行重复我们自己。我如何得到一个更干净的 API 来使用，这样我们就可以写:

```
class Order:
    price = NonNegative()
    quantity = NonNegative()

    def __init__(self, name, price, quantity):
        ... 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看 Python 3.6 中的[新描述符协议](https://docs.python.org/3/reference/datamodel.html#object.__set_name__):

*   `object.__set_name__(self, owner, name)`
    *   在创建所属类所有者时调用。描述符已被分配给名称。

使用这个协议，我们可以删除`__init__`并将属性名绑定到描述符:

```
class NonNegative:
    ...
    def __set_name__(self, owner, name):
        self.name = name 
```

Enter fullscreen mode Exit fullscreen mode

将所有代码放在一起:

```
class NonNegative:
    def __get__(self, instance, owner):
        return instance.__dict__[self.name]
    def __set__(self, instance, value):
        if value < 0:
            raise ValueError('Cannot be negative.')
        instance.__dict__[self.name] = value
    def __set_name__(self, owner, name):
        self.name = name

class Order:
    price = NonNegative()
    quantity = NonNegative()

    def __init__(self, name, price, quantity):
        self._name = name
        self.price = price
        self.quantity = quantity

    def total(self):
        return self.price * self.quantity

apple_order = Order('apple', 1, 10)
apple_order.total()
# 10 apple_order.price = -10
# ValueError: Cannot be negative apple_order.quantity = -10
# ValueError: Cannot be negative 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

Python 是一种通用编程语言。我喜欢它不仅有非常强大的功能，非常灵活，可能会极大地改变语言(例如元类)，而且有高级 APIs 协议来满足 99%的需求(例如描述符。)我相信有适合这项工作的工具。描述符显然是将行为绑定到属性的合适工具。尽管元类可能做同样的事情，描述符可以更好地解决这个问题。看到 Python 为更好地服务普通人的需求而发展也是令人高兴的。

这是我的结论:

1.  Python 3.6 是迄今为止最伟大的 Python。
2.  描述符用于将行为绑定到访问属性。