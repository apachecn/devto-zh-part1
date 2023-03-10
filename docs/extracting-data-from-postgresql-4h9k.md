# 从 PostgreSQL 提取数据

> 原文：<https://dev.to/caramelomartins/extracting-data-from-postgresql-4h9k>

PostgreSQL 有一个非常好的模块，专门用于提取文件和表之间的数据。

这可以用来提取三种格式的数据:-文本- CSV-二进制

有时，您可能希望将数据提取到不同于数据库所在的文件夹。您可能希望将数据提取到您登录的机器上。这可以通过使用`\copy`而不是`COPY`来完成。

提取数据的基本用法的一个例子是，在 CSV:

```
\copy (SELECT * FROM foo WHERE bar IS NOT NULL) TO '/path/to/folder' HEADER CSV 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的代码片段对于简单快速地生成报告或提取数据进行分析非常有用。

* * *

来源:-[https://www.postgresql.org/docs/9.5/static/sql-copy.html](https://www.postgresql.org/docs/9.5/static/sql-copy.html)

*最初发布于[Hugo Martins . io](https://hugomartins.io/)2016 年 10 月 10 日。*