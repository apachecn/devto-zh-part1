# 测试和测试驱动开发——为什么？

> 原文：<https://dev.to/wtfox/testing-and-test-driven-development---why>

我是纳什维尔一个叫 Penny 大学的组织的成员。本质上，它是一个“门户开放”的用户群，将想要学习的人和想要教学的人联系在一起。通常在午餐、咖啡或早餐时。我主动提出会见一些想要了解更多关于测试驱动开发的人，这很快成为一个热门话题。我被要求组织另一个关于 TDD 的圆桌讨论，事情是这样的。这不是一个低层次的代码语法概述，而是一个一般性的讨论，涉及一些很好的主题，如:

*   (有时)看不见的好处
*   典型工作流程
*   有用的实践
*   当你不应该测试的时候
*   以及如何用 TDD 的方式思考问题。

## (有时)看不见的好处

对于测试新手来说，无论是项目经理、你自己，还是同事，有时候都感觉是在浪费时间。“为什么我不能现在就写代码，然后一了百了呢！?"，你可能会问。这种情绪并不少见。当然，如果你的老板说不要写测试，那么你将不得不做他/她最终想要的，然后继续前进。(虽然我可能会以书面形式得到)对你的代码库进行测试，至少在某种程度上，表明你不仅仔细考虑了你的代码，而且比那更深入一点。如果你正在构建一个其他人正在使用的库，拥有一个良好的测试覆盖率可能会让他们对你的库更有信心。如果纯粹是内部的 app/库/什么的，那么有一个好的测试覆盖会让你对以后的重构有信心。你可以随心所欲地删除功能，完全重写它们或者删除它们，如果你有足够的覆盖率，你可以非常自信地说，如果所有的测试都通过了，那么在产品中就不会出现任何问题。

## 典型工作流程

你以前总是写测试吗？一个典型的 bug 修复或者特性请求看起来像什么？显然，我们都希望能够遵循红绿重构原则。这意味着，似乎只有 rockstar 开发人员能够首先编写失败的测试，然后编写通过测试的代码，然后重构代码使之优雅。嗯，我不是一个摇滚明星，我想大多数人都会同意，我们所能给予的就是我们最好的。

如果不努力做得更好，我们能做什么？如果足够简单，当然，我会遵循 TDD 原则，首先编写测试。这通常发生在小的错误修复中。另一方面，如果这是一个特点，对我来说是不可能的，但我会做一些事情来弥补。我通常做的是开始对我试图实现的东西进行编码，并在此过程中编写测试占位符。这很容易做到，只是一个函数和传递语句。也许一个文件字符串可以告诉我更多的信息。我会提交它，但是在那些测试更加充实之前，我不会提交那个 PR 来评审。(如果你担心你会忘记它们，让它们故意失败)这不是纯粹的 TDD，而是适应测试的功能设计的认知。这往往会带来最佳实践。当然，并不总是这样。有时候测试更像是一门艺术而不是科学。例如，如果我正在编写一个小费计算器，我可能会首先关注功能的实现，然后编写一个快速测试框架，如下所示:

```
class TipCalcTestCase(TestCase):
    # setup funcs
    ...

    def test_tip_calculates_correct(self):
        """ Test that get_tip_amount() should take a total and a percent 
        then return the amount needed to tip.
        """
        pass 
```

Enter fullscreen mode Exit fullscreen mode

## 有用的做法

单元测试应该测试一个单元。这听起来有些多余，但我基本上是说单元测试应该只测试一件事。如果我有一个又长又笨拙的函数，测试起来可能会是一场噩梦。通过使用这个函数并提取更小、更易读的函数，我可以轻松地测试每一行。因此，在编写代码时牢记 TDD 可以帮助您编写更简洁的代码。当然，我们的测试对真正的价值、代码可读性和易维护性来说只是一个副作用。将你的一个长函数提取成更小的、更易管理的函数，将会防止你在几个月后重新访问这些代码时产生很多心理摩擦。

## 嘲讽

有时候测试是多余的或者不必要的。例如，我不建议测试第三方库。你可以测试你如何调用它们，甚至测试它们*得到*调用，但是测试第三方库的内部应该由那些库的作者来处理。举一个愚蠢的(完全是临时想到的)例子，假设我正在编写一个帮助收银员的应用程序。我可能有如下所示的代码。在这种情况下，杂货是一个非常好的第三方库，它有一个方法，当给定一个项目(香蕉、饼干等)时，返回价格。问题是我不关心食品杂货的工作。我相信《杂货》的作者已经为我做了他需要做的事情。我想测试`get_subtotal()`，但我也想让这个测试完全离线运行。嘲讽(基本上只是告诉一个函数/类手动做什么)帮助我得到我需要做的，也就是测试`get_subtotal()`。

```
import groceries  # 3rd party lib 
def get_subtotal(items):
    subtotal = 0
    for item in items:
        # groceries.get() makes an API call
        item = groceries.get(item)
        subtotal += item.price

     return subtotal 
```

Enter fullscreen mode Exit fullscreen mode

还有测试...

```
import unittest
import mock

import get_subtotal

class SubtotalTestCase(unittest.TestCase):
    # Setup testcase class.. 
    ...

    @mock.patch('get_subtotal', return_value=7)
    def test_get_subtotal(self):
        items = ['banana', 'hot dog buns']
        # Since get_subtotal is mocked, it will only return 7 and do nothing else.
        result = get_subtotal(items)
        self.assertEqual(result, 14) 
```

Enter fullscreen mode Exit fullscreen mode

这些傻傻的例子是 100%没有经过测试的(哈！)现实生活中，只是基本概念。总的来说，我们玩得很开心，每个人都觉得他们带走了以前不知道的东西。如果我们最终再次讨论 TDD，这肯定会通过 google hangouts 记录下来。如果你有任何问题、想法、担忧、赞扬、责备等，请联系我们。

在我们的谈话中提到的几个链接:

*   [干净代码:敏捷软件技术手册](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_3?ie=UTF8&qid=1489003265&sr=8-3&keywords=test+driven+development)
*   [使用 Python 进行测试驱动的 Web 开发](https://www.amazon.com/Test-Driven-Development-Python-Harry-Percival/dp/1449364829/ref=sr_1_8?ie=UTF8&qid=1489003265&sr=8-8&keywords=test+driven+development)

在我的个人博客@[http://anthonyfox.io/](http://anthonyfox.io/)上阅读更多帖子

## 更多关于佩妮大学

“便士大学”这个名字是指早期英国牛津的咖啡馆。这些咖啡馆与欧洲启蒙时代有着重要的联系。花一便士，学者和普通人都可以进入咖啡馆，享受无穷无尽的咖啡供应，更重要的是，通过与同龄人的交谈来学习。因此，这些咖啡馆被称为“便士大学”。

我们新成立的团体 Penny University 将渴望学习的人与愿意分享知识的人联系起来，以现代的方式继承了这一古老的传统。这当然可以在咖啡馆，但也可以在任何其他地方，包括只是一个快速的谷歌闲逛。

没有导师或被辅导者，没有领导者或追随者。我们都是同龄人，我们在这里既是学习也是教学。

想了解更多？点击[这里](//www.pennyuniversity.org)