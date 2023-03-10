# Apache Cassandra 的方解石适配器

> 原文：<https://dev.to/michaelmior/a-calcite-adapter-for-apache-cassandra-30f>

对于那些不熟悉的人来说， [Apache 方解石](https://calcite.apache.org/)是一个通用的 SQL 查询优化器，它可以在多个后端数据源上执行 SQL 查询。这是一个强大的概念，因为它允许复杂的查询在提供从 [CSV 文件](https://calcite.apache.org/apidocs/org/apache/calcite/adapter/csv/package-summary.html)到 [MongoDB](https://calcite.apache.org/apidocs/org/apache/calcite/adapter/mongodb/package-summary.html) 的简单接口的数据源上执行。方解石也被用作基于成本的优化器框架，用于 Apache Hive 数据仓库。

我的大部分博士研究都围绕着为 NoSQL 数据库生成优化的模式，比如 Apache Cassandra T1。(对于概念验证工具，请查看 [NoSQL 模式评估器](https://github.com/michaelmior/NoSE)。)在发现方解石时，这似乎很适合我的工作。使用 NoSQL 数据库进行复杂查询的挑战之一是必须在查询语言设置的限制内工作。在之前的工作中，我在 Cassandra 上构建了一个非常简单的查询执行，旨在执行一组预定义的查询计划。利用方解石，可以在任何已定义的数据源(方解石称之为“适配器”)上执行非常完整的 SQL 方言[。](https://calcite.apache.org/docs/reference.html)

不幸的是，方解石没有为卡珊德拉适配器。幸运的是，编写适配器是一个相当简单的过程，所以我决定接手这项工作。适配器最简单的实现是提供一组表和一个扫描操作符来检索表中的所有行。虽然这足以让方解石执行查询，但在 Cassandra 中扫描表格[效率非常低](http://www.myhowto.org/bigdata/2013/11/04/scanning-the-entire-cassandra-column-family-with-cql/)。这是因为 Cassandra 表中的分区通常是通过哈希分区跨节点分布的。虽然可以检索所有的行，但是它们将以随机的顺序产生，并且查询需要联系数据库中的所有节点。假设用户想要发出的查询不需要触及表中的所有行，可以使用 Cassandra 查询语言中的过滤( [CQL](http://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlReferenceTOC.html) )将过滤下推到 Cassandra。

适配器的当前版本还支持通过[聚集键](https://docs.datastax.com/en/cql/3.0/cql/ddl/ddl_compound_keys_c.html)利用 Cassandra 表的本地排序顺序。还有很多工作要做，但是这个适配器的初始版本应该会在方解石 1.7.0 中发布。在发布之前，你必须从源代码编译[。下面是一组快速运行的命令。](https://github.com/apache/calcite/) 

```
$ git clone https://github.com/apache/calcite.git
$ cd calcite
$ mvn install

# You will need to create a JSON document which provides connection information
# An example can be found in ./cassandra/src/test/resources/model.json
$ ./sqlline
sqlline> !connect jdbc:calcite:model=path/to/cassandra/model.json admin admin 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以编写引用 Cassandra 表的 SQL 查询。注意，表名需要用引号括起来，某些查询模式可能会出现一些错误。您可以通过在`sqlline` shell 中为查询添加前缀`EXPLAIN PLAN FOR`来查看建议的计划。这将显示查询是否能够直接利用 CQL 中的过滤或排序。这离使 Cassandra 成为一个可行的数据仓库还有很长的路要走，但是它可能有助于执行偶尔的分析查询，而不需要编写大量的代码。

## 更新时间:2016 年 3 月 27 日

方解石 1.7.0 现已[发布](https://calcite.apache.org/docs/history.html#v1-7-0)，其中包括 Cassandra 适配器。除了上面讨论的内容，适配器现在还可以自动识别[物化视图](https://www.datastax.com/dev/blog/new-in-cassandra-3-0-materialized-views)。[文档](https://calcite.apache.org/docs/cassandra_adapter.html)可在方解石网站上获得。