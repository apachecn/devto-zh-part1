# ELI5:为什么要强制转换为接口？

> 原文：<https://dev.to/alephnaught2tog/eli5-why-cast-to-an-interface-1gi>

所以，我是 OOP 和 Java 的超级新手——确切地说，刚刚结束了第一学期的学习。

我理解接口类似于契约，并且理解它们的用途；同样，我了解选角等。我知道你可以对一个接口进行强制转换，但是我不明白你为什么要这么做。我查了一些书，一些网站，阅读了一些关于 SO 主题的论点，但还没有真正明白这样做的意义。

具体来说:如果你有一个名为`Forecastable`的接口和两个类`Weather`和`StockMarket`，这两个类都实现了`Forecastable`——因此*必须有*接口的方法，因此在它们中实例化的任何对象都可以访问这些方法——那么如果它们都已经可以固有地访问这些方法，那么将某个`Weather`或`StockMarket`对象转换为`Forecastable`的实际用途是什么呢？