# 在 PostgreSQL 中创建日期维度表

> 原文：<https://dev.to/duffn/creating-a-date-dimension-table-in-postgresql-5gjp>

如果你曾经读过 Ralph Kimball 的数据仓库书籍，或者有过用 Kimball 风格建模数据的经验，你就会知道日期维度是任何星型模式的关键部分。您可能找到了创建 Oracle、SQL Server 或 MySQL 的资源，但可能很难找到为 PostgreSQL 创建日期维度表的 SQL。这里有一段改编自 [PostgreSQL wiki](https://wiki.postgresql.org/wiki/Date_and_Time_dimensions) 的代码，我喜欢用它在 PostgreSQL 中创建必要的日期维度。