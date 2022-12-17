# 加速 Postgres 恢复第 2 部分

> 原文：<https://dev.to/adammckerlie/speeding-up-postgres-restores-part-2-226a>

#### 因为把恢复的时间减少一半还不够

<figure>[![](img/a9f8740b851ac4ba883d537fb90d69eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IKvedkSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACf4I9LTIdGTtyFTlqCFZKw.jpeg) 

<figcaption>显然毯子里有很多哈巴狗</figcaption>

</figure>

在[加速 Postgres 恢复的第 1 部分](https://procrastinatingdev.com/speeding-up-postgres-restores/)中，我谈到了我们如何缩短恢复本地环境所需的时间。最初我们开始天真地 pg_dump'ing(这是一个单词吗？)，gzipping，unzipping，然后使用`psql < file.sql`管道输出。完成完整恢复需要 30 多分钟。最后，我们使用了 [Postgres 的自定义格式](https://www.postgresql.org/docs/current/static/app-pgdump.html)，并使用了 job 的参数来将恢复加速到仅 16 分钟。

在本文中，我将概述我们如何减少备份的文件大小，从而进一步加快恢复速度。

#### 调查备份的大小

当我第一次写关于减少备份大小的文章时，压缩的备份大小大约是 2GB (30GB 未压缩)。随着时间的推移，我们的数据库几乎翻了一番(压缩了 3.7GB，未压缩了 68GB)。这意味着不仅恢复要花费更多的时间，传输文件也要花费两倍的时间。

了解到备份的大小已经翻倍，我开始尝试找出数据增长最多的原因和位置。

我做的第一件事实际上是找出数据库未压缩时的大小。

```
# SELECT pg_size_pretty(pg_database_size('dbname'));

pg_size_pretty
----------------
 68 GB
(1 row) 
```

接下来，我决定看看是否可以深入研究各个表的大小，看看是否有什么大问题。

```
# SELECT table_name, pg_relation_size(table_name), pg_size_pretty(pg_relation_size(table_name))
FROM information_schema.tables
WHERE table_schema = 'public'
ORDER by 2 DESC;

table_name | pg_relation_size | pg_size_pretty
-------------------+------------------+---------------
 logging | 19740196864 | 18 GB
 reversions | 15719358464 | 15 GB
 webhook_logging | 8780668928 | 8374 MB
 ... | 1994645504 | 1902 MB
 ... | 371900416 | 355 MB
 ... | 304226304 | 290 MB 
```

正如您所看到的，前 3 个表占整个数据库的 60%以上，它们都与历史或日志记录相关，这两者在恢复您的开发环境时通常是不必要的。当您包括这些表的索引(分别为 17GB、1GB、1.5GB)时，这些表占数据库的 89%。有了这些信息，我决定从 discovery 继续前进(89%的缩减对我来说已经足够了)，看看是否可以从我们的开发备份中排除这些表。

#### 最小化备份

每当我着手解决一个问题时，我喜欢从阅读项目文档开始。PostgreSQL 的文档非常棒，在通读了 [pg_dump 的](https://www.postgresql.org/docs/current/static/app-pgdump.html)文档几分钟后，我找到了我所需要的。

```
pg_dump dbname -Fc \
   --exclude-table-data 'logging*' \
   --exclude-table-data 'reversions*' \
   --exclude-table-data 'webhooks_logging*' > postgres.dev.sql 
```

*`*`用作通配符来删除这些表的索引。*

通过指定`--exclude-table-data`参数，我能够将我们的数据库大小从 3.7GB 压缩(68GB 未压缩)减少到 0.7GB 压缩(5.4GB 压缩)。

正如你在下面看到的，结果非常好。它加速了

**先前:**

```
$ pg_restore -d db -j 8 dumpfc.gz

real 16m49.539s
user 1m1.344s
sys 0m39.522s 
```

**现在:**

```
$ pg_restore -d db -j 8 devfc.gz

real 5m38.156s
user 0m24.574s
sys 0m13.325s 
```

如您所见，删除这些表会将总大小减少 89%,并将总体恢复时间加快 66%!如果您还记得第 1 部分，我们从 32.5 分钟的初始恢复时间开始。这意味着我们成功地将恢复时间缩短了 26.9 分钟以上，即 87%。

最终，我们的主动恢复时间从 16 分钟减少到了 5 分钟。**这为我们每年节省了额外的 57 小时恢复时间** (6 个设备，每年 52 次恢复，11 分钟)。我们总共节省了 130 个小时等待恢复的时间。

#### 最后的提示和想法

回到 [PostgreSQL 文档](https://www.postgresql.org/docs/current/static/populate.html)我们可以做很多事情来加快恢复速度。比如使用`pg_dump`上的`-j`参数来更快地备份我们的数据库(仅在 PostgreSQL 9.3+上可用)。禁用`autocommit`，增大`maintenance_work_mem`值或设置`max_wal_size`值更大。

此时，我对恢复我们本地环境所需的总时间感到满意。

* * *

*本帖最初发表于[procrastinatingdev.com](https://procrastinatingdev.com/speeding-up-postgres-restores-part-2/)T3】*