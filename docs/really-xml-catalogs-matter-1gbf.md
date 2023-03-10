# 真的，XML 目录很重要

> 原文：<https://dev.to/emceeaich/really-xml-catalogs-matter-1gbf>

早在 2000 年早期，我有一个博客，我在博客这个术语被创造出来之前就开始写了。

这是一篇文章，最初发表于 2004 年 1 月 16 日，内容是关于 XML 目录，以及如何确定应该测试什么。它非常流行，足以从 DOMXML 扩展的 PHP 文档中链接。

我已经更新了链接并改正了错别字。我才发现 Marc 的姓已经拼错很多年了！

在这篇文章发表大约四年后，[W3C 已经注意到了来自所有不使用目录或缓存的 XML 应用程序的所有验证请求流量](http://www.w3.org/blog/systeam/2008/02/08/w3c_s_excessive_dtd_traffic/)，并对此采取了一些措施。

这篇文章是一个时代的快照。还记得我们打算用 XML 做所有的事情吗？

—艾玛

* * *

本周，我了解到 XML 目录非常重要。

这开始于我在我的开发机器上更新马克·李亚纳的用于 Mac OS X 的 PHP 二进制文件。

页面渲染时间从几毫秒缩短到一分多钟。说我困惑是一种轻描淡写的说法。我回滚到早期版本。

#### 寻找线索

在另一台机器上进行的一些初步测试确定，速度下降是 PHP 的 DOMXML 扩展造成的。该扩展将 Gnome XML 和 XSLT 库作为函数和对象公开给 PHP。

在谷歌、php.net 和 xmlsoft.com 搜索之后，我给苏黎士的克里斯蒂安·斯托克发了一封电子邮件。Christian 从事 DOMXML 扩展，他可能知道一个 bug。

我认为问题在于嵌套 XInclude 语句。XInclude 是将一个 XML 文档包含在另一个文档中的规范。我们使用 XInclude 将其中一个站点的内容隔离成一个格式良好的有效 XHTML 文档，可以在 BBEdit 进行编辑。

内部网的一部分被描述为一个 Atom feed，[和包含在 feed 中的每篇文章的内容](http://web.archive.org/web/20031202023814/http://bitsko.slc.ut.us/blog/feed-data.html)。Atom 提要包含在一个[信封文档](http://www.xmlpatterns.com/EnvelopeMain.shtml)中，该文档包含呈现该部分中任何页面所需的其余 XML。

我得出的结论是，LibXML2 不知何故发生了变化，在解析嵌套的 XIncludes 时变得效率低下。

Christian 回信说他不知道有任何问题，但让我发送一个测试案例。

#### 错误的测试

我设计了测试用例:

foo.xml:

```
<?xml version="1.0" encoding="utf-8"?><foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include href="bar.xml" /></foo> 
```

bar.xml

```
<?xml version="1.0" encoding="utf-8"?><bar xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include href="baz.xml" /></bar> 
```

baz.xml

```
<?xml version="1.0" encoding="utf-8"?><baz>Content!</baz> 
```

运行时使用:

```
<?php$dom = domxml\_open\_file ("foo.xml");$start1=gettimeofday(); $dom->xinclude ();$end1=gettimeofday(); $totaltime1 = (float)($end1['sec'] - $start1['sec']) + ((float)($end1['usec'] - $start1['usec'])/1000000); echo "Time to handle includes: $totaltime1<br>"; echo $dom->dump\_mem ();?> 
```

那应该返回:

```
<?xml version="1.0" encoding="utf-8"?><foo xmlns:xi="http://www.w3.org/2001/XInclude"><bar xmlns:xi="http://www.w3.org/2001/XInclude"><baz>Content!</baz></bar></foo> 
```

的确如此，但比我预期的要快。它计时不到一秒，而不是一分多钟。

我把 bar.xml 改成:

```
<?xml version="1.0" encoding="utf-8"?><bar xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include href="baz.html" /></bar> 
```

而 baz.html 是:

```
<?xml version="1.0"?><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"><html ><head>Untitled</head><body><p>New document</p></body></html> 
```

正如我所料，这确实花了几秒钟。

#### 正确的测试

这让我明白，PHP 使用的库版本之间的 XInclude 已经开始默认验证。

http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd 是一个繁忙的地方。试着加载看看。**我敢打赌，这个 URL 是加载的，因为很多人不知道他们的工具每次需要加载或验证时都会调用这个 URL。**

在 baz.html 注释掉 DTD 声明，并重新运行测试，可以恢复到早期的性能水平。但是，我不想注释掉文档中的 DTD 引用。

#### 前往星表

我给 Christian 写了回信，询问为 PHP 构建的 LibXML 是否支持 [XML 目录文件](http://www.cafeconleche.org/books/effectivexml/chapters/47.html)。

有了目录文件，我可以告诉我的验证处理器将任何对“[http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd](http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd)”的引用解析为本地文件。目录可以做得更多，但是 DTD 文件的本地分辨率非常重要。

Christian 回答说，默认情况下， [LibXML 在`/etc/xml/catalog`](http://xmlsoft.org/catalog.html) 查找目录。所以我在那里创建了一个目录。

```
<?xml version="1.0"?><!DOCTYPE catalog PUBLIC "-//OASIS//DTD Entity Resolution XML Catalog V1.0//EN" "http://www.oasis-open.org/committees/entity/release/1.0/catalog.dtd"><catalog > <public publicId="-//W3C//DTD XHTML 1.0 Transitional//EN" uri="file:///etc/xml/xhtml/DTD/xhtml1-transitional.dtd" /></catalog> 
```

我将“[http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd](http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd)”指向 Apache 可以读取的本地目录，将 DTD 文件的副本放在那里，然后再次尝试测试。没有没有验证时那么快，但是肯定更快，因为它不需要通过互联网来验证包含的文件。

所以你去那里，目录文件，真的很重要。我变乖了。

感谢克里斯蒂安为我指出了正确的方向。

最初发表于 2004 年 1 月 16 日的《whump.com 》(但现在没人去了),更新于 2020 年 2 月 11 日，作者是 ECH。