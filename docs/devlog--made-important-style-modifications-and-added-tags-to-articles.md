# 对文章进行了重要的样式修改并添加了标签

> 原文：<https://dev.to/ben/devlog--made-important-style-modifications-and-added-tags-to-articles>

这是我在博客中的第一篇文章。只要我觉得它有价值，我就会把它保存下来。更像是个人锻炼。除非有特别有趣的，否则我不会分享给 [@ThePracticalDev](https://twitter.com/ThePracticalDev) 。我做这个网站，也[蓉城](https://argohq.com)。我将为每一个写日志，但是在这个网站上做的工作更容易记录，因为实现更简单。

## 今晚

我们把文章的封面图片做得更短，这样文章的更多内容可以马上看到。封面图片是可选的，但即使它在那里，也不需要如此普遍。从编辑角度来说，我们认为封面图片最适合那些更有激情的帖子，更多的指导性帖子应该没有封面图片。主页和个人资料页面上显示的文章也发生了变化。我已经看到一些可能的调整需要发生，但我很高兴这个方向。

文章中的`tags`变量也是新的。您最多可以包含三个标签。这些将被用来收集和导航文章，就像标签一样。这个网站上的编辑器接受 markdown，并且被设计成与[杰基尔](https://jekyllrb.com/docs/frontmatter/)兼容，所以默认的现在看起来是这样的:

```
title: 
published: false
description: 
cover_image: 
tags: 
```

Enter fullscreen mode Exit fullscreen mode

## 往前走

起初，我主要关注性能和更广阔的架构视野。这些东西仍然很重要，但我认为我们将会比现在更多地转向设计和 UX。