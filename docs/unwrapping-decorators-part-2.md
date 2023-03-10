# 展开装饰者，第 2 部分

> 原文：<https://dev.to/rpalo/unwrapping-decorators-part-2>

# 快速回顾

上一篇文章，我写了 Python 中装饰者的基础知识。对于那些错过它的人，这里是最精彩的部分。

1.  装饰器放在函数定义之前，用于包装或添加额外的功能到函数中，而不会模糊给定函数的单一目的。
2.  它们是这样使用的:

```
@custom_decorator
def generic_example_function():
    # ...
    pass 
```

Enter fullscreen mode Exit fullscreen mode

1.  在定义装饰函数时，它应该将一个函数作为输入，并输出一个新的/不同的/修改的/包装的函数。

```
def custom_decorator(func):
    # *args, **kwargs allow your decorated function to handle
    # the inputs it is supposed to without problems 
    def modified_function(*args, **kwargs):
        # Do some extra stuff
        # ...
        return func(*args, **kwargs) # Call the input function as it
                                    # was originally called and return that 
    return modified_function 
```

Enter fullscreen mode Exit fullscreen mode

好吧。差不多够了。让我们进入正题吧！我将讨论传递参数给装饰者(a la Flask 的`@app.route('/')`)、堆叠装饰者和基于类的装饰者。

# 装饰者论据

您可以将参数传递给装饰者！不过事情变得有点复杂了。还记得一个基本的装饰函数是如何接受一个函数，定义一个新函数，然后返回它的吗？如果你有参数，你实际上必须动态地生成装饰器，所以你必须定义一个函数返回一个装饰器函数，这个函数返回你真正关心的函数。好极了。Go go 小工具代码示例！

```
from time import sleep

def delay(seconds): # The outermost function handles the decorator's arguments 
    def delay_decorator(func): # It defines a decorator function, like we are used to 
        def inner(*args, **kwargs): # The decorator function defines the modified function
            # Because we do things this way, the inner function
            # gets access to the arguments supplied to the decorator initially
            sleep(seconds)
            return func(*args, **kwargs)

        return inner  # Decorator function returns the modified function 
    return delay_decorator # Finally, the outer function returns the custom decorator 
@delay(5)
def sneeze(times):
    return "Achoo! " * times

>>> sneeze(3)
(wait 5 seconds)
"Achoo! Achoo! Achoo!" 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍，一开始看起来可能会令人困惑。你可以这样想:在这个例子中，最外层的函数`delay`表现为当你添加装饰器时它被正确调用。一旦解释器读取了`@delay(5)`，它就运行延迟函数，并用修改后的返回装饰器替换`@delay`装饰器。在运行时，当我们调用`sneeze`时，看起来`sneeze`是用`seconds = 5`包裹在`delay_decorator`中的。因此，实际被调用的函数是`inner`，它被`sneeze`包装在一个 5 秒的休眠函数中。还在迷茫？我也是，有点。也许只是睡一觉，然后回来。

# 堆叠装饰工

我想换个更简单的话题，希望你继续在后台处理前一部分，到最后，它会神奇地变得有意义。我们拭目以待。让我们谈谈堆叠。我可以给你看。你会明白要点的。

```
def pop(func):

    def inner(*args, **kwargs):
        print("Pop!")
        return func(*args, **kwargs)

    return inner

def lock(func):

    def inner(*args, **kwargs):
        print("Lock!")
        return func(*args, **kwargs)

    return inner

@pop
@lock
def drop(it):
    print("Drop it!")
    return it[:-2]

>>> drop("This example is obnoxious, isn't it")
Pop!
Lock!
Drop it
"This example is obnoxious, isn't " 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，您可以包装已经包装好的函数。在数学中(实际上，在编程中)，*他们*会把这叫做**函数组合**。就像`f o g(x) == f(g(x))`一样，在`drop`上的`@lock`上叠加`@pop`会产生 pop(锁(掉(它))。休易会很骄傲的。

# 基于类的装饰者...

## ...没有争论

装饰器实际上可以由任何可调用的东西创建，例如任何提供`__call__`魔法方法的东西。通常，我试着想出我自己的例子，但是我在这里发现的很好地说明了正在发生的事情，我将通过最小的修改来窃取它。

```
class MySuperCoolDecorator:
    def __init__(self, func):
        print("Initializing decorator class")
        self.func = func
        func()

    def __call__(self):
        print("Calling decorator call method")
        self.func()

@MySuperCoolDecorator
def simple_function():
    print("Inside the simple function")

print("Decoration complete!")

simple_function() 
```

Enter fullscreen mode Exit fullscreen mode

哪些输出:

```
Initializing decorator class
Inside the simple function
Decoration complete!
Calling decorator call method
Inside the simple function 
```

Enter fullscreen mode Exit fullscreen mode

## ...带参数

基于类的装饰器使得装饰器参数变得更加容易，但是它们的行为与上面的不同。我再说一遍。

> 警告:基于类的装饰器根据它们是否有参数而有不同的行为。

我不确定为什么。应该有比我聪明的人来解释一下。不管怎样，当参数被提供给装饰器时，会发生三件事。

1.  装饰器参数被传递给`__init__`函数。
2.  函数本身被传递给`__call__`函数。
3.  `__call__`函数只被调用一次，而且是立即调用，类似于基于函数的装饰器的工作方式。

这里有一个例子，我答应偷偷放进去。它创建了一个简单的缓存装饰器，类似于本文的[中讨论的内置`@lru_cache`，除了你可以用输入/输出对预加载它。](http://assertnotmagic.com/2017/05/18/cache-me-outside.html) 

```
class PreloadedCache:
    # This method is called as soon as the decorator is attached to a function.
    def __init__(self, preloads={}):
        """Expects a dictionary of preloaded {input: output} pairs.
        I know it only works for one input, but I'm keeping it simple."""
        if preloads is None:
            self.cache = {}
        else:
            self.cache = preloads

    def __call__(self, func):
        # This method is called when a function is passed to the decorator
        def inner(n):
            if n in self.cache:
                return self.cache[n]
            else:
                result = func(n)
                self.cache[n] = result
                return result
        return inner

@PreloadedCache({1: 1, 2: 1, 4: 3, 8: 21}) # First __init__, then __call__ def fibonacci(n):
    """Returns the nth fibonacci number"""
    if n in (1, 2):
        return 1
    else:
        return fibonacci(n - 1) + fibonacci(n - 2)

# At runtime, the 'inner' function above will actually be called!
# fibonacci(8) never actually gets called, because it's already in the cache! 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧？我认为这个版本的创建装饰器是最直观的，至少对我来说是这样。

# 奖金！

额外的好处是，在 Python 中，因为函数是对象，所以可以给它们添加属性。因此，如果您修改上面的`__call__`方法，添加以下内容:

```
 def __call__(self, func):
        # ... Everything except the last line
        inner.cache = self.cache # Attach a reference to the cache!!!
        return inner

>>> fibonacci(10)
55
>>> fibonacci.cache
{1: 1, 2: 1, 4: 3, 8: 21, 3: 2, 5: 5, 6: 8, 7: 13, 9: 34, 10: 55} 
```

Enter fullscreen mode Exit fullscreen mode

[![Vigorous head banging](img/74f8c02e3f25d95768a73f9874a499d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bzhHNyB3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://assertnotmagic.com/img/decorator_head_bang.gif)

# 总结起来

不管怎样，我知道这很难接受。这个主题对我来说是比较令人困惑的 Python 主题之一，但是如果你正在创建一个库，它确实可以成为一个光滑的 API。看看 [Flask，一个 web 框架](http://flask.pocoo.org/)或者 [Click，一个 CLI 框架](http://click.pocoo.org/5/)就知道了。两个都是同一个团队写的，其实！实际上，[如果你感兴趣的话，我不久前写了一篇关于 Click](http://assertnotmagic.com/2016/11/27/discovering-click.html) 的简短帖子。

不管怎样，如果你有任何关于装饰者的问题(或者任何其他的问题)，不要犹豫问我！我总是很乐意提供帮助(尽管我通常在能够完全回答大多数问题之前就已经做了大量的谷歌搜索)。同样，如果你能比我解释得更好或者有额外的建议。ðŸ˜

*最初发布于[我的博客](http://assertnotmagic.com)。*
*编辑:将`@delay_function`改为`@delay`，之前不正确。感谢 [@radimsuckr](https://dev.to/radimsuckr) ！*
*再次编辑:将初始类示例中的`func`改为`self.func`。 [@radimsuckr](https://dev.to/radimsuckr) 让我保持诚实。*