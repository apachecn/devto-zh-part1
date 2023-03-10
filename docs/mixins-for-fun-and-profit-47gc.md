# 娱乐和利润的混合

> 原文：<https://dev.to/easyaspython/mixins-for-fun-and-profit-47gc>

这个帖子最初出现在 2016 年 12 月的 [Easy as Python](https://easyaspython.com/mixins-for-fun-and-profit-cb9962760556) 上。

* * *

有些人有时间、动力和资源完全从零开始做饭。他们会把面粉、水和鸡蛋做成意大利面，把奶酪、奶油和香料做成酱。这些人会做美味的食物，但这需要时间。你能想象如果他们自己磨小麦、养鸡和挤牛奶需要多长时间吗？

当创建软件时，有时我们应该达到的深度是有限的。但是，当我们想要实现的部分已经被其他人很好地执行时，重用它们是非常有意义的。我们可以购买面粉和屠宰牛肉，而不是碾磨小麦和饲养奶牛。我们可以从零开始，但不能从零开始。

让我们在面向对象编程中获得成功的一个方法是通过一个叫做 mixin 的概念。在 Python 中，mixins 通过[多重继承](https://en.wikipedia.org/wiki/Multiple_inheritance)得到支持。根据语言的不同，Mixins 有不同的形式，但是最终它们封装了可以在其他类中重用的行为。

使用真正的继承和使用 mixin 之间的界限是微妙的，但是它归结于一个事实，即 mixin 足够独立，以至于它感觉不到与父类相同。Mixins 通常不会单独使用，但也不是抽象类。

假设我们已经编写了大量 Python 软件，并且我们的应用程序已经准备好启动。我们正要按下按钮，这时有人走过来说“我们在记录什么样的事情？”*亲爱的上帝*，你想。*我们没有记录任何东西！*我们可以继续将以下样板文件添加到所有模块中，以开始日志记录:

```
import logging

class EssentialFunctioner:
    def __init__(self):
        ...

        self.logger = logging.getLogger(
            '.'.join([
                self.__module__,
                self.__class__.__name__
            ])
        )

    def do_the_thing(self):
        try:
            ...
        except BadThing:
            self.logger.error('OH NOES') 
```

这还不算太糟糕，但是想象一下对二三十个班级这样做。不好玩。mixins 能有什么帮助？看一看。让我们创建一个`LoggerMixin`来做和上面一样的工作:

```
import logging

class LoggerMixin:
    @property
    def logger(self):
        name = '.'.join([
            self.__module__,
            self.__class__.__name__
        ])
        return logging.getLogger(name) 
```

有了这个很好的封装，我们现在可以向现有代码中添加以下内容:

```
class EssentialFunctioner(LoggerMixin):
    def do_the_thing(self):
        try:
            ...
        except BadThing:
            self.logger.error('OH NOES')

class BusinessLogicer(LoggerMixin):
    def __init__(self):
        self.logger.debug('Giving the logic the business...') 
```

我们只编写了一次功能，但是现在我们可以在任何地方使用它！我们所要做的就是从`LoggerMixin`继承，然后我们可以继续使用`self.logger`，就好像我们已经在`EssentialFunctioner`和`BusinessLogicer`中设置了它一样！相当棒。

在某些地方，大量使用 mixins 可以节省大量时间或认知负荷。最近出现的一个用例是在 Django 项目中，其中几个基于类的视图只需要服务于我们的内部网的流量。一个现有的中间件做这种检查，如果一个请求来自网络外部，它就发出一个`Http404`。将该功能添加到我们的类中相当容易，在视图的`dispatch`方法上使用`decorator_from_middleware`和`method_decorator`:

```
from django.utils.decorators import decorator_from_middleware, method_decorator
from django.views import generic

from app import middleware

network_protected = decorator_from_middleware(
    middleware.NetworkProtectionMiddleware
)

@method_decorator(network_protected, name='dispatch')
class SecretView(generic.View):
    ... 
```

然而，当我添加第二个功能时，我也必须修饰这些视图。我对装饰有点厌倦了。

[![](img/67c2eaa0cf330fde9da908fed1a22f82.png)](https://cdn-images-1.medium.com/max/480/1*Pf-6CQy4ncyO-sxAtmVNTw.gif) 

<figcaption>节日快乐</figcaption>

相反，我将网络保护集成重构到一个 mixin 中，允许我们所有基于类的视图以它们认为合适的方式继承它:

```
from django.utils.decorators import decorator_from_middleware, method_decorator
from django.views import generic

network_protected = decorator_from_middleware(
    middleware.NetworkProtectionMiddleware
)

class NetworkProtectionMixin:
    @method_decorator(network_protected)
    def dispatch(self, *args, **kwargs):
        return super().dispatch(*args, **kwargs)

class SecretView(NetworkProtectionMixin, generic.View):
    ... 
```

类似的方法也可以用于 Django 的权限、认证等等。这是非常强大的，如果使用正确，可以产生非常有表现力的代码。它还降低了复杂性，允许更容易的单元测试和理解。

归根结底，mixins 是一种架构代码的枯燥方式，它抽象出潜在的复杂功能，这样开发人员就可以更专注于手头的任务，而不是支撑所有的部分。欢呼吧！

你在实践中使用过 mixins 吗？我想听更多。

#### 你可能也会喜欢[我在姜戈](https://easyaspython.com/django/home)的其他建筑故事。

* * *