# 我用 Csharp 做了一个简单的应用程序来 ping google

> 原文：<https://dev.to/mixmastermot/i-made-a-simple-app-to-ping-google-with-csharp-jo7>

# Ping 测试仪

我已经有一段时间了，但这是我的第一篇文章。关于我自己:
我用 Csharp 自学编程已经有一年了。

这个应用程序默认情况下 pings 谷歌，但它可以很容易地通过改变网址。你只需选择应用程序应该 ping 网站的次数，然后按下开始键，神奇的事情就发生了。该应用程序计算最高、最低和平均 ping 时间以及数据包丢失量。该应用程序在一个线形图中可视化最后 10 次 ping。

这是我第一次使用后台工作线程和委托。我真的很喜欢弄清楚如何使用这些概念来构建一个响应式应用程序。

我把它上传到 github 去看看[这里](https://github.com/MixMasterMot/PingTest)