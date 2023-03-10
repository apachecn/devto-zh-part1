# 正确地做新的事情

> 原文：<https://dev.to/rpalo/doing-new-things-right>

大约一周前，我想出了一个项目，在开始之前，我决定，不管其他人怎么看，我都想以正确的方式做事。这篇文章的主要目的是列出一些事情，并获得一些反馈(希望)关于我可以做对的事情。关于这个项目，我还想特别强调几件事:结构、测试、使用 Twitter API 和另一个很酷的 Python 包。为了每个人的理智，我会尽量说得简短些。

## 概述

这个项目叫做 [FanBot](https://github.com/rpalo/fanbot) 。这是一个非常简单的 Python 机器人，可以持续运行，并可以向目标用户发送随机称赞。此外，我刚刚完成了一个功能，允许某人在推特上@机器人，这促使它几乎立即称赞目标用户(除了标准的日常称赞之外)。目前，部署并不新奇，因为应用程序是关键的反义词。我只是把它设置在一个 [PythonAnywhere](https://pythonanywhere.com) shell 上运行，每当他们退回他们的服务器时就手动重启它。我知道如果我愿意，我可以做得更多。在这方面，我看不出有什么必要。

## 结构

就“做正确的事情”而言，我确保从一开始就将项目设置为 git 存储库。它有一个许可证文件、一个. gitignore 文件、requirements.txt(对于那些不懂 Python 的人来说，这类似于 Ruby 中的 Gemfile 或 JavaScript 中的 package.json)、一个测试目录和一个项目目录。还有一个示例主用户文件`main.py`。我确保我的自述文件有简短而有效的部分，包括功能、安装和使用说明、贡献建议和行为准则。我不确定我需要用多少最后两个，但是如果我要**把事情做对**，我要**把事情做对**。

## 测试

我花了一点时间把所有的测试都连接起来，因为这是我决定通过测试驱动开发的一个宽松版本来做的第一个项目之一。我和派特斯一起去的。在我的测试目录中有一个`__init__.py`文件(空白)和一个`test_fanbot.py`文件。随着我添加更多的功能，我可能不得不将它分成多个测试模块。使用 Pytest 的好处是，我不必对 python 路径耍什么花招。

```
from fanbot import fanbot # i.e. from .. (top level) import fanbot 
class TestFanBot:
    """Tests for the FanBot class"""

    def test_example(self):
        assert 2 + 2 == 4

    # Actual tests... 
```

Pytest 的自动检测使一切工作，您可以使用简单的断言语句。实际上我遇到了麻烦，因为它比我想象的要简单得多。

## Twitter API

一旦你开始使用 Twitter API，它就非常容易使用。你可以在项目自述文件中或者通过谷歌搜索“twitter api python”找到基本说明。还有几个很好的 Python Twitter API 客户端可以简化事情。我使用了 [Tweepy](http://tweepy.readthedocs.io/en/v3.5.0/getting_started.html) ，Tweepy 的文档也将帮助您入门。一旦你通过认证，你基本上可以要求任何你想要的。只要做好准备，在终端上打印任何东西基本上都是没有意义的，因为信息量太大了。

这是 Tweepy 入门页面中的示例。

```
import Tweepy

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)

api = tweepy.API(auth)

# This is the cool tech hipster way to check your twitter public_tweets = api.home_timeline()
for tweet in public_tweets:
    print tweet.text 
```

## 日程安排

好的。最后一件事，我保证。我充分利用了`schedule`包。很容易就`pip3 install schedule`。它们让编写人类可读的任务调度变得异常容易。检查一下。

```
import schedule

def bark():
    print("Woof")

schedule.every(10).minutes.do(bark)
schedule.every(4).days.at("12:15").do(bark)

while True:
    schedule.run_pending()
    time.sleep(30) # seconds 
```

就像这样，大约每十分钟或者每 4 天午餐时间，你就会得到一个吠叫终端。所以。简单。敬。阅读！我喜欢它。

## 总结起来

这就是我现在的处境。我愿意认为我做得很好，但是自学并且还没有开发工作的一个缺点是缺少一个团队在我没有以正确的方式做事情时对我大喊大叫并告诉我。这就是我崇拜的粉丝群的来源。如果你看到我在哪里做了不必要的奇怪或糟糕的事情，或者不必要的困难，或者我在哪里错过了机会，请告诉我。提前感谢！

*最初发布于[我的博客](https://assertnotmagic.com)。*