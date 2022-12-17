# 介绍:Inveinta -另一个库存管理工具

> 原文：<https://dev.to/seiyria/introducing-inveinta---another-inventory-management-tool-12hp>

几周前，我感觉没有效率，对我来说，消除这种感觉的最好方法是从头到尾敲定一个小项目。一些人谈到需要一个解决方案来管理大量库存+一些其他功能，如协作编辑和签入和签出他们的东西。我想做这个已经有一段时间了，但现在是时候敲定了。因此，我启动了一个快速离子项目，开始学习如何使用新的 Firestore 技术(对旧的实时数据库 IMO 的巨大改进),并开始工作。

我的想法是让你使用或创建“mixins”来分类你的条目列表，使用字段集合来定义你的列表。因此，如果你想出售 MTG 的交易卡，你可以选择“万智牌”。如果你想有一个视频游戏的登记/检查系统，你可以选择“视频游戏”“图书馆”。当然，您也可以制作自己的 mixins 没有办法满足所有可能的需求。

你可以在这里看到最终结果，但是因为这只是一个应用程序(我还没有时间为它做一个“合适的”网站)，这里有一些例子:

*   [我的桌游收藏](https://inveinta.netlify.com/#/p/9XjlXJGeM3AuP8LlBwBp)
*   [我的配方存储列表](https://inveinta.netlify.com/#/p/k9iaUn0iA1rCp5wN6cKx)

同样，[这里有一些图片](https://imgur.com/a/TW34N)的工具在行动中。

你怎么想呢?