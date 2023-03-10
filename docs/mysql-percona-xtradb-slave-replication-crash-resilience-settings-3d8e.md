# MySQL (Percona XtraDB)从复制崩溃恢复设置

> 原文：<https://dev.to/cosimo/mysql-percona-xtradb-slave-replication-crash-resilience-settings-3d8e>

自从我上一篇博文以来，已经是一个地质时代了！

哦，这期间发生了很多事情。在过去的四年里，我在新闻推荐系统的开发和运营部门工作，这个系统为 Opera Discover 提供了动力。有了足够的精力，我打算写一个推荐系统的“入门”系列。我们走着瞧。

同时，我想把这些笔记留在这里。它们有助于 MySQL 复制从网络不稳定、突然断开和一般数据中心故障中正常恢复。他们来了。

随着 MySQL 5.6 在 Debian Wheezy 上的发布，我们开始经历突然关机或突然机器崩溃后的 MySQL 复制崩溃。当系统重新启动时，mysql 复制通常会由于从属中继日志损坏而停止。

我开始研究这个问题，并很快发现了描述日志损坏问题以及 mysql 开发如何解决这个问题的文档和博客文章。以下是我用作参考的页面:

*   [MySQL 5 . 6 . 2 中的复制元数据](http://datacharmer.blogspot.com/2011/04/replication-metadata-in-mysql-562.html)
*   [崩溃安全 MySQL 复制](http://mysqlmusings.blogspot.no/2011/04/crash-safe-replication.html)
*   [使用 mysql 5.6 启用崩溃安全奴隶(Percona 博客)](https://www.percona.com/blog/2013/09/13/enabling-crash-safe-slaves-with-mysql-5-6/)
*   [Percona-服务器抗崩溃复制](https://www.percona.com/doc/percona-server/5.5/reliability/crash_resistant_replication.html)

此外，我们有一些(我认为无关的)mysql 元表无法查询的问题，尽管它们被列为存在于 mysql shell 和文件系统中。我们通过以下步骤解决了这个问题:

```
DROP TABLE innodb_table_stats;
ALTER TABLE innodb_table_stats DISCARD TABLESPACE;
stop mysql
rm -rf /var/lib/mysql/mysql/innodb_table_stats.*
restart mysql 
```

这些步骤必须按这个顺序执行，即使在删除一个表后修改它看起来是无意义的。这是荒谬的，因为有时 mysql 的东西。

### 崩溃安全复制设置

我们已经提炼出一套独立的复制设置，将提供年复一年无限的崩溃安全复制乐趣(也许)。它们在这里:

```
# More resilient slave crash recovery
master-info-repository = TABLE
relay-log-info-repository = TABLE
relay-log-recovery = ON
sync-master-info = 1
sync-relay-log-info = 1 
```

让我们看看这些设置的作用。

`master-info-repository=TABLE`和`relay-log-info-repository=TABLE`指示 mysql 将主日志信息和中继日志信息存储到 mysql 数据库中，而不是存储在单独的*中。`/var/lib/mysql`文件夹中的信息文件。

这很重要，因为在崩溃的情况下，我们希望确保主/中继日志信息符合数据库本身提供的相同 ACID 属性。推论:确保相关的元表将 InnoDB 作为存储引擎。

比如，一个`SHOW CREATE TABLE slave_master_info`应该说`Engine=InnoDB`。

如果从属系统上的中继日志文件损坏，则`relay-log-recovery=ON`非常重要。当 MySQL 在启动过程中遇到损坏的中继日志文件时，默认情况下它会丢球并停止。该选项设置为 on，将导致 mysql 尝试从 master 数据库中重新提取中继日志文件。然后，应该将主服务器配置为将它的二进制日志保存一段适当的时间(通常我使用 2 周，但实际上取决于数据库更改的数量)。作为测试，可以用损坏的副本替换当前的中继日志文件(例如来自`/dev/urandom`)。MySQL 将丢弃损坏的日志文件，并尝试从主服务器下载，之后将执行常规启动。**全自动恢复！**

`sync-master-info=1`和`sync-relay-log-info=1`支持在每次事务提交时将主日志信息和中继日志信息同步提交到数据库。这也是每个应用程序都必须评估的内容。最有可能的情况是，如果您有大量的写操作，您不想启用它。但是，如果写入率足够低，这个选项不会增加任何性能，相反，应该确保`slave_master_info`和`slave_relay_log_info`表始终与复制和数据库其余部分的状态一致。

仅此而已。我很乐意听到对这些信息的任何反馈或更正。