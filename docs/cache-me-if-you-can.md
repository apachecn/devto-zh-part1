# 有本事你来抓我

> 原文：<https://dev.to/krhancoc/cache-me-if-you-can>

*(X-Post from my Medium Blog-[Here](https://medium.com/python-pandemonium/cache-me-if-you-can-d9815c9e2b35#.feyokrsiq))*

你好世界！我想占用你一两句话，首先说这是我的第一篇博文。我希望通过这个博客，我可以探索我喜欢的想法和概念。希望能让我成长为一名计算机科学家和程序员，从我的错误(和成功)中也能让其他人成长。我的目标受众是任何会发现这很有用的人。在展示代码之前，我想简单介绍一下缓存。但是你可以在这里找到我的例子项目:[https://github.com/krhancoc/Blog-Example-1-Caching](https://github.com/krhancoc/Blog-Example-1-Caching)。

[![Cache!](img/29f43dbd3e487bf772a5f73d76d3cef4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_877_luH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Awh2lk27HwR_BK4sN4f4Vnw.jpeg)

缓存是一种优化技术，它将以前检索到的结果存储到您想要的位置(可能是本地内存、文件、SQL 数据库等)。这些结果可以是从整个页面到一系列 API 调用的输出。我喜欢将缓存(以及类似的记忆化)视为以空间复杂度为代价降低时间复杂度的东西。当你有一个 CPU 密集型的动作并且结果不会经常改变时，缓存是非常有用的。我将使用一个递归返回斐波那契数的例子(虽然实际上我只使用封闭形式，因为常数时间是炸弹)。但是递归地做对我们来说很好，因为以下几点:

1.  不使用任何缓存/记忆的斐波那契数列需要非常长的时间，并且很容易递归实现。

2.  斐波纳契数列在递归时包含许多相同的“子问题”。例:F(5)= F(4)+F(3)= F(3)+F(2)+F(2)+F(1)。你可以看到 F(2)被调用了两次，不需要太多思考你就可以想象得到对 F(800)的请求会导致成千上万次相同的递归调用。

注意使用递归方法时会出现一个问题 python 的递归限制。因为这个函数增长的速度很快，你会很快达到递归极限，现在你可以增加它(虽然不推荐)。可以做的一件正确的事情是通过在服务器启动时请求越来越大的斐波那契数来“预热”缓存。这样，当你的斐波那契站点的顾客出现时，他们的请求很可能已经在缓存中了。显然有很多方法可以解决这个问题，但我想稍微谈一下。

当我看到这里时，我受到了做这个小项目的启发，并知道我可以应用相同的技术为 Django 的一个函数创建一个缓存装饰器，我正在使用这个函数向一个 API 发送数千个请求(这反过来给我运行应用程序的服务器带来了很大的压力)。

这里是装饰者:

```
import functools

from django.core.cache import caches
from django.core.cache.backends.base import InvalidCacheBackendError

def cache(cache_alias='default'):
    class cacheobject(object):
        def __init__(self, func):
            self.func = func
            try:
                self.cache = caches[cache_alias]
            except InvalidCacheBackendError:
                self.cache = caches['default']
        def __call__(self, value):
            response = self.cache.get(value)
            if response is None:
                response = self.func(value)
                self.cache.set(value, response, None)
                return response
            else:
                return response

        def __repr__(self):
            '''Return the function's docstring.'''
            return self.func.__doc__

        def __get__(self, obj, objtype):
            '''Support instance methods.'''
            return functools.partial(self.__call__, obj)

    return cacheobject 
```

Enter fullscreen mode Exit fullscreen mode

您还需要为您的 Django 项目设置某种缓存系统，这可以在文档中找到(这真的很好)。这个项目看起来是这样的:

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'local',
    },
    'sql': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'location': 'table',

    },
    'file': {
        'BACKEND':   'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': 'file_cache'
    }

}
CACHE_MIDDLEWARE_SECONDS = 300
CACHE_MIDDLEWARE_KEY_PREFIX = ''
CACHE_MIDDLEWARE_ALIAS = 'default' 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以将它添加到您喜欢的任何函数中，并将您的缓存参数更改为您想要存储它的缓存的别名，这取决于您需要的缓存速度以及您要存储的数据。更持久的数据应该存储在 SQL 缓存中(较慢的选项也可能占用带宽)，而不太持久的数据应该存储在系统内存中，这非常快)，文件缓存位于两者之间。

对于我们的斐波纳契例子，它会是:

```
@cache('default')
def fibb(num):
    if num in (0,1):
        return num
    else:
        return fibb(num -1) + fibb(num - 2) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我创建了一个简单的 Django 模板，让用户请求一个斐波那契数。

现在看看记忆和缓存的力量吧！曾经需要几分钟才能找到第 30 个斐波那契数的函数，现在基本上是即时的(由于线性时间复杂度的良好降低)。从这里开始，当您用越来越大的数字预热缓存时，您将能够找到第一千个斐波那契数，而不是进行“1.071509e+301”个单独的函数调用，它可以减少到数千个，甚至一个函数调用(取决于缓存的内容)。

> f(1000)= 4346655768693745643568852767504062580256466051737178040

我意识到这仍然非常笼统，但是我希望这是一个模板，其他人可以在他们的 Django 项目中使用，以减少某些功能对他们的服务器 CPU 或带宽使用的负载。如果有机会，尝试使用和不使用缓存装饰器来拉动和测试函数，并尝试使用不同的缓存进行速度比较。

[![Smashing](img/56f6fef2bf923f5d8d1d5d9e0ef4bc20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X6sJZoRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A1mC2UE9AVCkgzypbysL33w.jpeg)

干杯伙计们。以防万一，这里又是 git 回购的链接:

[https://github.com/krhancoc/Blog-Example-1-Caching](https://github.com/krhancoc/Blog-Example-1-Caching)。

如果你觉得还有更多要补充的，不要犹豫，评论或联系我，一个开放的讨论有利于每个人的学习。

谢谢你，瑞恩。