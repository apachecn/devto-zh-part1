# C#中表达式主体集合属性初始化问题

> 原文：<https://dev.to/dance2die/expression-bodied-collection-property-initialization-gotcha-in-c-6nc>

*这是 2017 年的帖子，但当我重新学习时，我读了一遍，并在这里提供了它🙂*

我实现了一个 [trie，](https://en.wikipedia.org/wiki/Trie)，这是一个树形数据结构，通常用于存储搜索字符串。因为它是一棵树，所以它有一个“Children”来保存子节点。

但是后来我遇到了一个问题，简单地调用将孩子添加到集合(第 9 行)不起作用。