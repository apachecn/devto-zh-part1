# 周五爆炸#10

> 原文：<https://dev.to/horia141/friday-blast-10-4mdf>

这是星期五爆炸系列的第十部分。由于突发的健康问题，我上周错过了。所以今天的剂量比平时大一点。尽情享受吧！

[每个软件工程师应该知道的关于搜索的事情(2017)](https://medium.com/startup-grind/what-every-software-engineer-should-know-about-search-27d1df99f80d) -关于为 web 应用程序设置搜索的更长阅读。想想 Kayak、Booking、AirBnB 中的搜索，而不是人们可能在博客上遇到的普通文档搜索。潜在的主题是，这是一个*难*和*尚未解决的*问题。解决这个问题需要一个专门的团队和专门的系统，以及大量的定制。即使有现成的解决方案，如 [ElasticSearch](https://www.elastic.co/) 。

[不要让你的 CDN 背叛你——使用 sub resource integrity(2017)](https://hacks.mozilla.org/2015/09/subresource-integrity-in-firefox-43/)——sub resource integrity 是一种非常廉价的方式，可以为你的 webapp 增加一个严重的安全提升。它旨在检查所请求的资源实际上是您在某个设置中想要的资源，其中一些资源可能来自 CDN 或其他您不完全信任的第三方。您只需要向您的`script`和`link`添加一个`integrity`属性，它包含文件预期内容的散列。如果浏览器下载的任何内容具有不同的散列，则该资源将被阻止加载到页面中。当然，主页需要由你自己的系统提供服务，任何可以 MITM 的人都可以取消完整性检查，但你会遇到更大的问题。

[CMS 终极指南-第 1 部分(2016)](https://www.webdesignerdepot.com/2016/11/the-ultimate-guide-to-cmss-part-one/) 和[第 2 部分](https://www.webdesignerdepot.com/2016/12/the-ultimate-guide-to-cms-part-2/)-CMS 领域概述[CMS](https://en.wikipedia.org/wiki/Content_management_system)-最常见的 web 应用(以及一般应用)之一。

[std::visit 是现代 C++ (2017)](https://bitbashing.io/std-visit.html) -这是我离开 C++开发的原因之一。有些任务看起来很容易解决，但是需要对模板和特性有深入的掌握。那个和[三个字母](https://en.wikipedia.org/wiki/Digraphs_and_trigraphs)。不过对于高性能的 C++来说很有趣。

[POSIX I/O 有什么不好的(2017)](https://www.nextplatform.com/2017/09/11/whats-bad-posix-io/) -对于高性能计算来说，问题似乎是 POSIX 为读写规定的相当强的一致性模型。在本地机器上，很容易保证如果您从一个进程写入一个文件，以后从其他进程读取将会看到该写入。而不管 OS 采用的缓存层或其他优化。在分布式环境中提供这种东西是非常昂贵的，而且对于许多应用程序来说并不需要。这篇文章关注的是针对这个问题提出的解决方案。

[Spark 的位置敏感散列法(2016) #talk](https://www.youtube.com/watch?v=Ha7_Vf2eZvQ&feature=youtu.be) -优步如何基于[位置敏感散列法](https://en.wikipedia.org/wiki/Locality-sensitive_hashinge)构建分布式重复乘车检测系统。LSH 是一种哈希方案，它试图根据相似性度量将“相似”的对象映射到同一个哈希桶。所以与常规哈希相反，用于查找。这意味着相似的对象将被分组在一起，并且 O(N < sup > 2)相似性计算可以减少到一个更小的簇。

[不听描述性统计(2017)](http://debrouwere.org/2017/02/01/unlearning-descriptive-statistics/) -众所周知，许多统计测量的中心性、扩散、偏斜、相关性等。对真实世界的数据集来说并不那么有用。相反，它们适用于高斯数据，从数学上分析它们是可以的。本文详细讨论了这个问题，提供了一些例子和替代方案。

[Dropbox 如何安全存储你的密码(2016)](https://blogs.dropbox.com/tech/2016/09/how-dropbox-securely-stores-your-passwords/)——深入了解 Dropbox 如何存储密码。这里有很多纵深防御。当然，密码是用每个用户的 salt 与`bcrypt`一起存储的。但是还有一种叫做*全局胡椒*的东西，这是一种很难得到的用于加密散列密码的值。他们的计划是将这种胡椒存储在服务器的安全硬件模块中。

[量化个人数据的信息内容(2017)](https://www.johndcook.com/blog/2017/09/12/quantifying-the-information-content-of-personal-data/) -这篇文章是对从各种个人数据中获得的信息位的粗略计算——性别、邮政编码、年龄等。当一个人的年龄特别高时，知道他的年龄和邮政编码就足以识别这个人，这很好。