# 数据迁移

> 原文：<https://dev.to/m1pko/data-migration>

我即将开始一个新的数据迁移项目。这是我目前的事情。我基本上必须将一个数据模型从 Oracle 11g 数据库迁移到 Oracle 12c 数据库中的另一个数据模型，并且在这两者之间考虑所有业务规则和周围的应用程序生态系统及其所有限制和挑战。

在我之前的项目中，我们从 11g 迁移到 11g。我们使用 PL/SQL 提取并执行一些数据准备。核心数据转换随后在 C++模块中执行，从中生成 XML 文件并最终导入。这远不是一个好的解决方案；相反，它基本上是过去项目的一些代码返工。

你们呢？我很期待知道你对此类项目有什么样的体验！

图片来源:雷纳特拉([https://www.flickr.com/photos/renatela/1069651852/](https://www.flickr.com/photos/renatela/1069651852/))via[visual hunt](https://visualhunt.com/re/6c9922)/[CC BY-NC-ND](http://creativecommons.org/licenses/by-nc-nd/2.0/)