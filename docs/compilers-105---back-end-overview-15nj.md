# 编译器 105 -后端概述

> 原文：<https://dev.to/lefebvre/compilers-105---back-end-overview-15nj>

一旦前端完成了它的工作，后端组件就可以接管了。

这是我的编译器系列的第五篇，也是后端的第一篇。我在这些文章中介绍了编译器的前端部分:

[编译器 101–概览和词法分析器](https://dev.to/lefebvre/compilers-101---overview-and-lexer-3i0m)
[编译器 102–解析器](https://dev.to/lefebvre/compilers-102---parser-2gni)
[编译器 103–语义分析器](https://dev.to/lefebvre/compilers-103--semantic-analyzer-540k)
[编译器 104–IR 生成](https://dev.to/lefebvre/compilers-104---ir-generation-39e8)

## 后端

后端的组件获取由前端的最后一步(IR 生成)生成的 IR，并发出可执行代码，在 Xojo 的情况下，这是机器语言。

本系列的其余文章将讨论与 LLVM 相关的后端。具体来说，这些组件是:

*   【计算机】优化程序
*   代码生成
*   连接物

敬请期待！