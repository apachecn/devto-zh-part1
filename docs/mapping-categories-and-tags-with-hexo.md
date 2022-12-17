# 用 Hexo 映射类别和标签

> 原文：<https://dev.to/danieljsummers/mapping-categories-and-tags-with-hexo>

DJS 咨询技术博客今天从[杰基尔](////jekyllrb.com)转移到[赫索](////hexo.io)(现在也是[开源](////github.com/danieljsummers/techblog.djs-consulting.com))。在结束转换时，我遇到的最后一个问题是如何处理类别和标签，这些类别和标签不一定有可以从名称自然派生的 slug。以“C#类”为例。这个类别的标准 slug 应该是`c`，这是一种完全不同的编程语言；有趣的是，“C++通常也会得到它的 slug 作为`c`。

在 Hexo 默认的`_config.yml`文件中，有两个空项，分别名为`category_map`和`tag_map`；他们的评论暗示了一个映射，但是我找不到这些条目的正确语法。我们跳上 Hexo [Gitter](////gitter.im) 聊天室，问了这个问题，有人给我们指出了这个问题。要定义映射，在`category_map`或`tag_map`顶层项目下创建一个项目。这个网站的地图，就像现在这样，看起来像这样:

```
category_map:
  C++: c-plus-plus
  C#: c-sharp
  .NET: dot-net
tag_map:
  c#: c-sharp
  .net: dot-net 
```

Enter fullscreen mode Exit fullscreen mode

正如你在侧边栏(在博客上)的链接中看到的，“编程>。NET > C#以一个以`/programming/dot-net/c-sharp/`结尾的 URL 结束，这正是我们要找的。

*本帖最初发表于[techblog.djs-consulting.com](https://techblog.djs-consulting.com/2017/mapping-categories-and-tags-with-hexo.html)T3】*