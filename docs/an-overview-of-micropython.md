# MicroPython 概述

> 原文：<https://dev.to/kenwalger/an-overview-of-micropython>

当一个人开始探索令人兴奋的物联网领域时，有很多选择可供选择。不仅有许多微控制器的选择，而且有多种语言来为这些设备编程。很多时候，微控制器被销售为依赖于 C 或 C++语言，这很好，但我不知道这两者中的任何一个。在 [thingsSDK](http://thingssdk.com/) 上，有一些由 [Andrew Chalkley](https://www.twitter.com/chalkers) 领导的关于使用 [JavaScript](https://www.javascript.com/) 语言的伟大而令人惊叹的工作，如果 JavaScript 是你的语言，我强烈推荐看看他们的工作。也有很多使用 Java 进行物联网的资源，例如 Nathan Tippy 就是一个很好的资源。

所有这些都是很棒的语言，但是如果你是一个对物联网感兴趣的初级开发人员怎么办？或者如果你是一个 python 主义者，不想再学习另一种编程语言怎么办？嗯，对于许多微控制器来说,**是**Python 选项，可以说在许多方面比 Java、JavaScript、C 或 C++更容易学习。那个选项就是 [MicroPython](http://micropython.org/) 。

MicroPython 是一个小型的开源 Python 编程语言解释器，可以在嵌入式主板上运行。它最初是由剑桥大学的 Damian P. George 博士于 2013 年开发并于 2014 年发布的。它实现了 [Python 3](http://www.python.org) ，并且是专门为在资源有限的设备上运行而设计的。

与物联网世界中的许多事情一样，特定语言的使用在很大程度上受到可用工具、库和软件包的影响。MicroPython 附带了一个交互式读取-评估-打印循环(REPL ),用于快速原型开发，并且还完全支持将 MicroPython 脚本加载到设备上运行。它支持一个广泛的基于 Python 的库，对于低级操作，可以根据需要用 C/C++函数进行扩展。

物联网初学者开始的许多任务，如闪烁的灯、读取开关、驱动伺服系统等。在 MicroPython 中很容易做到。此外，它通常非常容易阅读，而且都是基于 Python 的。例如，如果一个人将 MicroPython 安装在一个 [NodeMCU ESP8266](https://www.amazon.com/gp/product/B01N0B48NI/ref=as_li_tl?ie=UTF8&tag=kenwalgersite-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=B01N0B48NI&linkId=dc1e9f82e2beb42bca36616a22b8820d) 板上，这是一个在物联网中开始工作的流行且廉价的板，将一个基本的 led 灯连接到 [GPIO 引脚 5](https://iotbytes.wordpress.com/nodemcu-pinout/) ，打开灯(高状态)和关闭灯(低状态)非常简单。

```
import machine

led = machine.Pin(5, machine.Pin.OUT)
# Turn on LED
led.high()
# Turn off LED
led.low()

```

machine 模块包含在 MicroPython 中，处理来自软件的许多硬件连接。它允许访问 GPIO 引脚、CPU 频率、I2C 总线等。如果在硬件上是低级的，那么*机器*模块是最好的选择！

可以想象，将 Python 3 中的所有东西都塞进一个微控制器是不可能的，但是对嵌入式系统有意义的核心数据类型和模块包括在内。另一个限制是，由于 Python 是一种高级编程语言，它不像 C 或 C++那样快速或精简。虽然许多操作不受此影响，但是如果您的应用程序要求时间紧迫或者性能至关重要，那么使用 MicroPython 可能是一个问题。然而，如前所述，在这些情况下，可以扩展 MicroPython 来使用 C/C++。

由于 MicroPython 是开源的，请在 GitHub 上查看它，人们可以进一步扩展或适应它特定的用例场景。例如，如果您需要在引导时运行特定的东西，您可以将它包含在闪存到设备的包中。非常方便。

希望我至少激发了您对使用 MicroPython 进行物联网的兴趣，让您进一步了解它。这个开发站点实际上有一个实时的交互设备，你可以在这里尝试一些代码，并看到它在真实世界的设备上运行。试试吧，在下面留下评论，与他人分享你的所作所为！

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在[我的博客](https://www.kenwalger.com/blog)上查看原始帖子。*