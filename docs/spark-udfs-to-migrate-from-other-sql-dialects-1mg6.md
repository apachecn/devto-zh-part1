# 激发 UDF 从其他 SQL 方言迁移

> 原文：<https://dev.to/wrschneider/spark-udfs-to-migrate-from-other-sql-dialects-1mg6>

*本文最初出现在[我的博客](http://wrschneider.github.io/2017/12/05/spark-udf-for-sql.html)上*

我发现创建 Spark UDFs 很有帮助，可以更容易地从另一个数据库(如 SQL Server)迁移 SQL 中的逻辑。

SQL Server 定义了几个类似`LEN`、`REPLACE`和`CHARINDEX`的字符串函数，这些函数在 Spark 中是默认没有的。幸运的是，这些很容易在 Spark 中用 UDF 实现:

```
spark.udf.register("len", (s: String) => s.length())
spark.udf.register("replace", (orig: String, toReplace: String, replaceString: String) => orig.replace(toReplace, replaceString))
spark.udf.register("charindex", (substring: String, str: String, startPos: Int) => str.indexOf(substring, startPos - 1) + 1) 
```

Enter fullscreen mode Exit fullscreen mode

这些都是围绕原生 Scala 字符串函数的瘦包装器。这些现在可以在 Spark SQL 查询中使用:

```
select len('foo bar') as len_test,
     replace('foo bar baz', 'bar', 'quux') as replace_test,
     charindex('.', '1.2.3', 3) as idx_should_be_4,
     charindex('.', '1.2.3', 0) as idx_should_be_2,
     charindex('@', '1.2.3', 0) as idx_should_be_0 
```

Enter fullscreen mode Exit fullscreen mode

如果语法是标准的，这使得从 SQL Server 向 Spark 复制粘贴查询变得更加容易。

一个缺点是 Spark UDFs 是函数，而不是方法，因此[不允许默认参数值](https://stackoverflow.com/questions/25234682/in-scala-can-you-make-an-anonymous-function-have-a-default-argument)。所以你必须明确地添加一个`0`作为`CHARINDEX`的第三个参数。