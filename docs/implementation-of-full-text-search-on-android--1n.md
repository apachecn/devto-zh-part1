# Android 平台上全文搜索的实现

> 原文：<https://dev.to/node/implementation-of-full-text-search-on-android--1n>

## 方案一:用 Lucene

*   卢斯[http://lucene.apache.org/](http://lucene.apache.org/)

推荐版本是 4.7 带 Java 6。

## 方案 B:使用 SQLite FTS3/FTS4

*   SQLite FTS3 和 FTS4 扩展[http://www.sqlite.org/fts3.html](http://www.sqlite.org/fts3.html)

```
1\. Introduction to FTS3 and FTS4
1.1\. Differences between FTS3 and FTS4
1.2\. Creating and Destroying FTS Tables
1.3\. Populating FTS Tables
1.4\. Simple FTS Queries
1.5\. Summary
2\. Compiling and Enabling FTS3 and FTS4
3\. Full-text Index Queries
3.1\. Set Operations Using The Enhanced Query Syntax
3.2\. Set Operations Using The Standard Query Syntax
4\. Auxiliary Functions - Snippet, Offsets and Matchinfo
4.1\. The Offsets Function
4.2\. The Snippet Function
4.3\. The Matchinfo Function
5\. Fts4aux - Direct Access to the Full-Text Index
6\. FTS4 Options
6.1\. The compress= and uncompress= options
6.2\. The content= option
6.2.1\. Contentless FTS4 Tables
6.2.2\. External Content FTS4 Tables
6.3\. The languageid= option
6.4\. The matchinfo= option
6.5\. The notindexed= option
6.6\. The prefix= option
7\. Special Commands For FTS3 and FTS4
7.1\. The "optimize" command
7.2\. The "rebuild" command
7.3\. The "integrity-check" command
7.4\. The "merge=X,Y" command
7.5\. The "automerge=N" command
8\. Tokenizers
8.1\. Custom (Application Defined) Tokenizers
8.2\. Querying Tokenizers
9\. Data Structures
9.1\. Shadow Tables
9.2\. Variable Length Integer (varint) Format
9.3\. Segment B-Tree Format
9.3.1\. Segment B-Tree Leaf Nodes
9.3.2\. Segment B-Tree Interior Nodes
9.4\. Doclist Format
Appendix A: Search Application Tips 
```

*   添加搜索功能[https://developer.android.com/training/search/index.html](https://developer.android.com/training/search/index.html)

> 2.1 设置搜索界面
> 了解如何向您的应用添加搜索界面，以及如何配置活动来处理搜索查询。
> 
> 2.2 存储和搜索数据
> 学习一种在 SQLite 虚拟数据库表中存储和搜索数据的简单方法。
> 
> 2.3 保持向后兼容
> 了解如何保持搜索功能向后兼容旧设备。

*   Android 中的全文搜索示例[https://stack overflow . com/questions/29815248/full-text-search-example-in-Android](https://stackoverflow.com/questions/29815248/full-text-search-example-in-android)