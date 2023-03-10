# Fitbit Ionic 开发简介

> 原文：<https://dev.to/ripsup/intro-to-developing-for-the-fitbit-ionic-bao>

## 是什么让你开始在 Fitbit Ionic 上开发？

如果您想了解更多开发细节，请跳到下一部分。

我现在的雇主有一个健康计划，在这个计划中，你可以通过做不同的任务并跟踪它来获得每年 500 美元的收入。我在公司的第一年(2016 年)网站上的一切都是极其人工的，很难跟踪。

去年，对于大多数挑战，他们使用的软件转向了跟踪步骤，而不是时间。他们还给了我们从 Fitbit 直接购买的任何商品 60 美元的折扣。由于 Fitbit 在年初进行销售，这意味着我能够以 70 美元的价格获得人力资源监控费用 2。虽然我通常不戴手表，或者以前从未对智能手表感兴趣，但这款设备确实引起了我的兴趣。我马上开始考虑如何对它进行定制，以获得我想要的附加功能。不幸的是，这是不可能的，我被困在它所提供的，但错误是种植在我的头上。

随着 Ionic 的发布，还发布了为该设备编写应用程序的功能，这立即引起了我的注意(加上 GPS、防水等附加功能)，我准备好投入资金了。我决定等到新年，以防我的工作提供另一个折扣，我希望会有一个新年销售。所以在一月的第二周，我下了订单，两天后就收到了。

在等待了大约一个小时来设置设备(不知道为什么最初需要 30 分钟来下载设备的更新，但它做到了)，我打开了入门指南，10 分钟后，我的手表上运行了一个应用程序。

## 你用什么写的？

app 是用 Javascript，SVG，CSS 写的。从文档来看，我们似乎可以访问该设备的每个传感器和输入，但显然我还没有达到这一点。

文档保存在这里- [Fitbit 开发者网站](https://dev.fitbit.com/)

有趣的是，你需要使用他们自己的基于 web 的 IDE 来编写/构建/部署你的应用。你不能做本地开发然后推送到你的设备上。也没有本地仿真，因此您必须在实际设备上测试您的工作。你的手表还必须连接到 wifi，才能为它推送一个版本，这给你想在远程某个地方使用你的应用程序增加了一个烦恼。

## 你能用它做什么？

现在看起来你可以用这款设备做很多事情，因为除了侧面的按钮，你还有一个完整的触摸屏可以使用。你可以调用 web 来获取数据(推送数据，我还不确定，因为在文档中不是很清楚。)像控制智能设备这样的事情从你的手腕上就可以很容易地完成。通过访问所有的传感器数据，我敢打赌你可以创建一些有趣的用户界面交互，你可以用你的手臂操纵东西。

有人开发了一个条形码应用程序，我用它来进入当地的基督教青年会。该设备内置了 NFC 来处理触摸支付，但我还没有找到任何关于我们是否可以将它用于其他事情的文档。

## 接下来是什么？

我计划做的第一个项目是一个滑块拼图，只是为了让自己习惯使用该设备。我会试着在这里写一篇构建日志并分享它是如何进行的。我还将开放源代码，供其他感兴趣的人查看。

对于你认为用这款设备做起来很酷的事情，你有什么问题或想法吗？下面分享！感谢你的阅读。

我觉得有趣的是，我花了 10 分钟让一个应用程序在我的新设备上运行，却花了 4 天时间来写这篇文章。:)