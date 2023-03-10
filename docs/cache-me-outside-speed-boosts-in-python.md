# 在外面缓存我:Python 中的速度提升

> 原文：<https://dev.to/rpalo/cache-me-outside-speed-boosts-in-python>

在阅读了这篇关于用周期表元素造字的博客文章后，我突然发现了一点小智慧。我强烈推荐阅读。然而，这不是我们在这里的原因。我们在这里缓存(# sorrynotsorry)Python 标准库的一些好处。

对于那些不知道的人来说，缓存对于用相同的参数重复调用一个函数，并且每次都能得到相同的结果时非常有用。不是每次都计算结果，而是将结果保存在缓存中以备后用。这对于本质上需要长时间运行的函数特别有用，比如 API 调用、文件 I/O 或超级嵌套循环。考虑一下我们谦逊的朋友斐波那契函数。在 Python 中，一个常见(但非常低效)的版本是这样的:

```
def fibonacci(n):
    """Finds the nth fibonacci number."""
    if n in [1, 2]:
        return 1
    else:
        return fibonacci(n - 2) + fibonacci(n - 1) 
```

Enter fullscreen mode Exit fullscreen mode

它看起来真的很好，也很容易理解。但是想象一下它甚至以`n = 5`的速度运行。下面是调用栈的粗略版本:

```
fibonacci(5)
    fibonacci(3)
        fibonacci(1) = 1
        fibonacci(2) = 1
        = 2
    fibonacci(4)
        fibonacci(2) = 1
        fibonacci(3)
            fibonacci(1) = 1
            fibonacci(2) = 1
            = 2
        = 3
----------------
2 + 3 = 5. 
```

Enter fullscreen mode Exit fullscreen mode

这还只是第五位。你可以看到`fibonacci(2)`是如何被调用*三次的！*想象一下如果你试图计算`n = 10`或`n = 100`会发生什么！别担心。我做到了。事实上，对于`n = 100`来说，我太无聊了，等着它结束。事实上，我甚至等不及`n = 50`结束了！甚至`n = 35`也花了 7 秒左右。这就是缓存的用武之地。你可以很容易地制作自己的。在这篇博文中，我尝试了一个版本。如果我自己这么说的话，结果很好。向函数添加缓存的唯一问题是，它占用了大量的代码行，并且掩盖了您实际想要显示的函数正在做的事情。也有办法解决这个问题，但是既然标准库(*小号大张旗鼓*)已经覆盖了你，为什么还要和这个问题斗争呢？

看哪。

```
 # You should really look at the functools docs.
# I mean it, there's some awesome stuff in there!
# Also, FYI, LRU stands for Least Recently Used
# which is the item that gets booted from the cache when
# it hits its limit 
from functools import lru_cache

@lru_cache(maxsize=128) # Works best with maxsize as a power of 2. def fibonacci(n):
    """Finds the nth fibonacci number."""
    if n in [1, 2]:
        return 1
    else:
        return fibonacci(n - 2) + fibonacci(n - 1) 
```

Enter fullscreen mode Exit fullscreen mode

注意到我甚至没有改变主函数吗？我只是对自己说，“哦，这里有缓存可能会很好，”并在上面撒了一点装饰。我将不得不在另一篇文章中讨论装饰器，但是要知道它们是用来扩充功能的，通常是用来添加副作用的，比如在某个地方注册一个函数，或者像你所期望的那样，添加一个缓存。我们获得了多少利益？

无缓存:

```
$ python3 -m cProfile fib.py -n 40
102334155
         204668315 function calls (7 primitive calls) in 89.028 seconds 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。带缓存:

```
$ python3 -m cProfile fib.py -n 300
222232244629420445529739893461909967206666939096499764990979600
         323 function calls (24 primitive calls) in 0.001 seconds 
```

Enter fullscreen mode Exit fullscreen mode

怎么样？

*最初发布于[我的博客](http://assertnotmagic.com) :)*