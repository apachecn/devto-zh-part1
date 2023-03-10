# 元编程 Python -缺少方法

> 原文：<https://dev.to/rpalo/metaprogramming-python---method-missing>

我正在读一本名为[元编程 Ruby](https://pragprog.com/book/ppmetr2/metaprogramming-ruby-2) 的书，作者是 Paolo Perrotta，真的很有趣！我学到了很多。当我阅读的时候，我时常会遇到一种技巧，我对自己说，“这很棒！不知道能不能用 Python 之类的另一种语言做类似的事情？”经过一些研究，我想我应该和其他人分享一个这样的发现。但首先，一些背景。

## 背景:什么是元编程？

元编程是一个很好的东西。不确定)动态语言能做什么。本质上，它是一堆不同的编写代码的方法，在运行时，它们会为你编写代码。让我们来看看我的意思。

```
>>> class Dude:
...     def __init__(self):
...         pass
...
...     def sup(self):
...         return "Sup, brah!"
>>> d = Dude()
>>> dir(d)
[ ...(lots of built-in methods, etc.), 'sup'] 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？在运行时，我们只是窥视一个对象，可以看到它的方法！继续走。

```
>>> if 'sup' in dir(d):
...     d.name = "Brad"
... else:
...     d.name = "Chad"
...
>>> d.name
'Brad' 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经根据相同对象的方法和属性修改了我们的对象。我们的代码导致了代码被编写(一点点)。感受力量。

## 技术:方法缺失

如果你熟悉 Ruby，你可能知道 Ruby 对象有一个名为`method_missing`的内置方法，如果你调用一个在这个特定对象中不存在的方法，这个方法就会被调用——这是可以预见的。你可以用它做各种事情。一种是在运行时根据你无法控制的事情动态生成方法。它节省了样板代码和不断的重构。我们需要一个例子来让事情变得更清楚。

### 场景:疯狂的 API 设计师！

假设你和一个喝了太多咖啡的队友一起工作。他们管理您的程序使用的 API，但是他们不断添加和更改端点名称。布局总是一样的，所以至少这是可以预测的。例如，上周一，主资源是`beets`，所以你在你的`Supermarket`类上实现了一个`get_beets()`方法。该方法获取库存中当前的`beet`对象...

```
class Supermarket:
    def __init__(self, api_root):
        self.api_root = api_root

    def get_beets(self, *args, **kwargs):
        url = f'{self.api_root}/beets/'
        return self.api_get(url, *args, **kwargs)

    def api_get(self, url, *args, **kwargs):
        ...
        # Imagine this method hits the api with the provided arguments
        print(f'{url} called with args: {args}')  # for this example 
```

Enter fullscreen mode Exit fullscreen mode

[![Beets by Dwight](img/07bc734f1c14303b13f84d6d2b476112.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xxOvU0ww--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sswqqo14040xzrlxsbso.jpg)

至少...直到雅虎决定使用更具描述性的对象名:`beetroot`。所以，你回到你的代码，更新方法名和 API 调用。然后，他们动态地添加`rootabegas`，即使你的大部分代码都是一样的。但是！你狡猾，你聪明，而且你知道**元编程**！因此，你达到了你的`Supermarket`的`__getattr__`方法。

```
import re

class Supermarket:
    def __init__(self, api_root):
        self.api_root = api_root
        self.pattern = re.compile(r'get_([a-z]+)')  # matches any get_[something] call 
    def __getattr__(self, method_name):
        match = re.match(self.pattern, method_name)
        if match:
            def temp_method(*args, **kwargs):
                url = f'{self.api_root}/{match.group(1)}/'
                return self.api_get(url, *args, **kwargs)
            return temp_method
        else:
            raise AttributeError(f'No such attribute: {method_name}')

# Let's test it! >>> s = Supermarket('example.com')
>>> s.get_beetroots(3)
'example.com/beetroots Called with args: (3)'
>>> s.this_should_error()
...
AttributeError: No such attribute: this_should_error 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？让我们击中要害:

1.  `__getattr__`是 Python 对象中的一个内置方法，如果 Python 找不到您想要的方法或属性，就会调用这个方法。我们一会儿会详细讨论这个问题。但是 Python 期望这个方法要么返回一个要调用的函数，要么像正常情况一样引发一个 AttributeError。正如你所看到的，这就是我们上面所做的。
2.  我们定义了一个[正则表达式](http://www.rexegg.com/regex-quickstart.html)来匹配我们期望的 API 调用方法模式。我们不知道对象/端点会是什么，但是我们知道它会以“get_”开始，以对象名结束。
3.  传递被调用的方法的名字。如果方法名与我们的正则表达式匹配，我们就进入第 4 步。否则，我们继续引发 AttributeError。这在《T1》的最后一次 REPL 通话中得到了体现。
4.  如果方法调用与我们的正则表达式匹配，我们希望构建并返回一个函数来调用。你怎么称呼这个内部函数并不一定重要。如果我们决定将它永久添加到我们的类中，它可能会，但我们现在不这样做。这个函数展示了我们希望避免的样板文件。它应该接受我们期望像`get_beets`这样的函数接受的参数。
5.  `match.group(1)`返回正则表达式中第一组(也是唯一一组)括号中的项目，这恰好是我们关心的对象的名称。
6.  最后，我们返回函数，它会立即被用户最初指定的参数调用。

在一个完美的世界中，我们可能应该让`Supermarket`获取一个可用端点的列表，这样如果用户对不可用的端点进行 API 调用，我们就可以提醒用户。这也可能有助于安全一些。我将把它留给读者作为练习。

## 压倒`__getattr__`的方法

我为你准备了两个可能的场景。两者有相同的解决方案。

### 场景:示例方法或覆盖

如果您想设计一个示例方法，让阅读您代码的人可以看到一个动态生成的方法的示例，该怎么办呢？或者。如果你想重写一个方法，定义你自己的行为。两者都是可能的，因为`__getattr__`只有在 Python 搜索对象的期望属性之后才会被调用。看一看。

```
class Supermarket:
    ...
    def get_squash(self, *args, **kwargs):
        return "NO.  NO SQUASH.  It is the devil's gourd."

    def get_peanuts(self, *args, **kwargs):
        """
        Example of a dynamically generated API call method
        created by __getattr__
        """
        url = f'{self.api_root}/peanuts/'
        return self.api_get(url, *args, **kwargs)

>>> s = Supermarket('localhost')
>>> s.get_squash()
"NO.  NO SQUASH.  It is the devil's gourd."
>>> s.get_peanuts()
'localhost/peanuts called with args: []'
>>> s.get_bananas('big ones')
'localhost/bananas called with args: ["big ones"]'
>>> s.soup()
...
raise AttributeError(f'No such attribute: {method_name}') 
```

Enter fullscreen mode Exit fullscreen mode

一切按计划进行！

## 告诫元

您应该知道，伴随着强大的元编程能力而来的是巨大的元编程责任。这种事情，如果没有良好的布局和干净的实现，会使代码非常难以阅读、推理和调试。如果你发现自己写的注释比解释对象如何工作的代码还多，你也许应该在元上放松一下。事实上，我在几个地方读到过，如果您不确定是否需要元编程，您可能不需要。如果你非常确定你需要它，你仍然可能不需要。但是，在特定的情况下，比如当你需要一堆几乎完全相同的方法，而你事先不知道应该存在哪一个时，它会非常强大，可以省去你很多维护、麻烦和打字的工作。

总的来说，试一试，也许它会成为对你有用的提示！让我知道它怎么样了，ðÿ˜

* * *

另外，我真的推荐你去看看我在本文开头提到的那本*元编程 Ruby* 的书。

如果你发现自己在想“这些 F 弦是什么(例如`f'WHAT IS THIS'`)”，不要害怕。它们是 Python 最新的字符串插值/格式化语法。我计划在不久的将来写一篇关于他们的博文。它们类似于 JavaScript 的新`#{syntax}`。

* * *

*封面艺术:[谢恩·威利斯](https://www.photo.net/2277921#//Sort-Newest/All-Categories/All-Time/Page-0)T3】*

*最初发布于[我的博客](http://assertnotmagic.com)T3】*