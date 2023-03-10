# 使用 Python 离线编程

> 原文：<https://dev.to/jadolg/programming-while-offline-with-python>

我住在古巴。我从出生起就住在古巴，古巴(相信我)对软件开发人员来说就像是氪石。作为互联网渗透率较低的国家之一(只有 32.5%的人口可以访问互联网)，与平均月收入相比，互联网价格非常高，这使得大多数时间很难与外界联系。想要更多细节？尽管如此，我们有很棒的大学，每年有成千上万的人毕业于科技行业。我就是其中之一。对我打击较大的一件事是，有时不得不在如此糟糕或昂贵的互联网接入下使用在线资源，因为我喜欢用 Python 编程，以下是我所做的。
我正在编写一个项目，我需要从一个在线服务中获取一些数据，这很烦人，因为我正在做一些丑陋且难以维护的事情，比如:

```
def doit():
    return 'false response'

    text_from_internet = get_data()
    return text_from_internet 
```

Enter fullscreen mode Exit fullscreen mode

当我最终上线并准备测试我的工作时，我评论了错误的回报:

```
def doit():
    # return 'false response' 
    text_from_internet = get_data()
    return text_from_internet 
```

Enter fullscreen mode Exit fullscreen mode

太难看了，我都不好意思给你看我在做什么。但我没有停下来。在使用这个丑陋的修复程序一段时间后，由于我偏离了注释/取消注释我的垃圾代码行的路线，出现了各种各样的问题，我决定找到一个更好的方法来做这件事，并编写了我自己的装饰器来处理这种问题:

```
def offline_sandbox_value(value):
    def decorator(func):
        @wraps(func)
        def inner(*args, **kwargs):
            if SANDBOX:
                logging.debug('** Sandboxed ' + func.__name__ + '() with value: ' + value)
                return value
            else:
                return func(*args, **kwargs)

        return inner

    return decorator 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来有点简单，但这段代码节省了我几个小时的工作。这是一个装饰器，用于在我离线时为我的函数设置默认值。想象一下第一个函数，如果我要离线尝试我的项目，y 可以定义`SANDBOX = True`并用一个假的响应来装饰我的函数:

```
SANDBOX = True

@offline_sandbox_value('false response')
def doit():
    text_from_internet = get_data()
    return text_from_internet 
```

Enter fullscreen mode Exit fullscreen mode

然后每当我在线运行代码时，就将沙盒切换到`False`。如果我对沙盒化所有东西感兴趣(即使我有访问权)，这是很有用的，但是如果我想检查资源的可用性，并且在无法访问给定 URL 时沙盒化方法，该怎么办呢？好吧，让我们这样做:

```
def offline_sandbox_value(value, url=None):
    def decorator(func):
        @wraps(func)
        def inner(*args, **kwargs):
            if url:
                if connect_to(url):
                    return func(*args, **kwargs)
                else:
                    logging.debug('** Sandboxed '+func.__name__+'() with value: '+value)
                    return value

            elif SANDBOX:
                logging.debug('** Sandboxed ' + func.__name__ + '() with value: ' + value)
                return value
            else:
                return func(*args, **kwargs)

        return inner

    return decorator 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以把一个 URL 传递给我的装饰者，如果我做了，我不必为沙盒定义一个值。所以我的代码应该是这样的:

```
@offline_sandbox_value('false response', url='https://im-online.herokuapp.com/')
def doit():
    text_from_internet = get_data()
    return text_from_internet 
```

Enter fullscreen mode Exit fullscreen mode

因为我在各种项目中使用它，它对我非常有用，所以我决定把它打包并上传到 PyPi，命名为 [offline_sandbox](https://pypi.python.org/pypi?name=offline_sandbox&version=1&:action=display) ，这样其他人也可以使用它。GitHub 上也有[这里](https://github.com/jadolg/offline_sandbox)你想投稿吗？打开一个问题或拉请求:-)感谢您的阅读。