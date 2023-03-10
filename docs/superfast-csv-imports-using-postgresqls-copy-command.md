# 使用 PostgreSQL 的 COPY 命令超快速导入 CSV

> 原文：<https://dev.to/petarcurkovic/superfast-csv-imports-using-postgresqls-copy-command>

*本帖最初发表在[倾覆的八博客](https://infinum.co/the-capsized-eight/superfast-csv-imports-using-postgresqls-copy)T3 上*

在 web 应用程序中处理各种数据源需要我们创建从 CSV、Excel 和其他文件类型中提取信息的服务。在这种情况下，最好使用一些现有的库，或者如果你的后端在 Rails 上，使用 gems。像[`CSVImporter`](https://github.com/pcreux/csv-importer)[`Roo`](https://github.com/roo-rb/roo)这种特色非常酷的宝石还有很多。但是你也可以用普通的 [`Ruby CSV`](http://ruby-doc.org/stdlib-2.0.0/libdoc/csv/rdoc/CSV.html) 。

无论哪种方式，如果这些是小的 CSV 文件，你会很容易地完成你的工作。但是如果需要导入大的 CSV 文件(~100MB / ~1M 行)呢？

[![Postgres COPY](img/0dcaf75f67d542eea4d2a68676830144.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dqrjg7wB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r5mjhydxlvwtb4j5x3i7.png)

我发现 PostgreSQL 有一个非常强大但非常简单的命令，叫做 [`COPY`](https://www.postgresql.org/docs/9.6/static/sql-copy.html) ，它可以在文件和数据库表之间复制数据。
它有两种用途:

*   **将数据从 CSV 文件导入数据库**
*   **将数据从数据库表导出到 CSV 文件**。

用法示例:

```
COPY forecasts
FROM 'tmp/forecast.csv'
CSV HEADER; 
```

Enter fullscreen mode Exit fullscreen mode

这段 SQL 代码将 CSV 文件中的内容导入到我们的`forecasts`表中。注意一件事:假设**表中列的数量和顺序与 CSV 文件**中的相同。

### 结果

导入一个大约 1M 行的 CSV 文件现在只需要不到 4 秒钟的时间**，与以前的解决方案相比，这已经非常快了！**

更多详情，请阅读[原博文](https://infinum.co/the-capsized-eight/superfast-csv-imports-using-postgresqls-copy)。