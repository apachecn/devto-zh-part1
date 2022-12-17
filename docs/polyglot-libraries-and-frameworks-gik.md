# 多语言库(和框架)

> 原文：<https://dev.to/horia141/polyglot-libraries-and-frameworks-gik>

名称 *Polyglot Library* 是我为那些出现在不止一种编程语言中的库推荐的名称。这方面最著名的例子可能是 xUnit 系列测试框架。虽然它们都是用不同的语言编写的，但是有一个毋庸置疑的静态概念核心，以及版本之间的“影响树”。

测试是这类库的沃土，有 mockito、selenium 等。被移植到许多不同的编程语言和平台上。

非常相关的是实现“外部”规范的库。许多模板语言都属于这一类，事实上， [mustache](http://mustache.github.io/) 拥有超过 40 种不同的编程语言实现。多亏了相当小的特性集和必需的 API，实现看起来相当相似。我还放了协议缓冲区、节俭、Avro 等东西。或者这个 bucket 中的各种 JSON 解析库，PCRE，re2 等等。也是。

在更广的层面上，我们可以说库成为了一个独立于编程语言的实体。鉴于越来越多的编程语言似乎开发了类似的[特性集](http://horia141.com/string-interpolation.html)，用你选择的语言来表达一个成功的库将变得越来越容易。

理想情况下，应该有一个这样的库的主列表。编程语言设计团队的部分工作是将这些库移植到新的平台上。虽然提高了实现的门槛，但它将从根本上降低早期采用者的门槛，因为他们可以在一个地方找到所有他们喜欢的库。