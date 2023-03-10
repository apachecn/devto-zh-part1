# MySQL 兔子洞:数据恢复的冒险

> 原文：<https://dev.to/cyberomin/mysql-rabbit-hole-adventure-in-data-recovery>

最近我的一个老项目神秘死亡——我不这么认为，事情很少神秘死亡，一定是出了什么问题；MySQL 没有运行，可能会丢失数据。更令人沮丧的是，我拖延数据库复制和备份的时间最长。这都是我的错。

在过去的几个月里，我收集了大量的数据，我不认为自己会再次经历收集这些数据的过程，因此，借口不是一个选项。我不得不用我所掌握的最少的信息来恢复它。

要开始这个过程，我需要首先在一个新的 MySQL 实例上重新创建数据库。

```
CREATE DATABASE children DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER ’nimo'@'%' IDENTIFIED BY ‘password';
GRANT ALL PRIVILEGES on children.* TO ’username'@'%'; 
```

Enter fullscreen mode Exit fullscreen mode

第一个错误是我从`/var/lib/mysql`目录中删除了一些文件。事后看来，如果我备份了这个目录的内容，我的恢复之旅会少很多痛苦，但事后看来，他们说是 20-20

我搞砸了，做了一件任何正常人都不应该做的事情，我在生产服务器上运行了`sudo rm`。原来，MySQL 需要这些文件； *ibdata1* ， *ib_logfile1* ， *ib_logfile0* 为了正常工作，如果您尝试恢复 InnoDB 表，这一点尤其重要。`ibdata1`、`ib_logfile1`、`ib_logfile0`文件是 InnoDB 存储引擎的核心。`ib_logfile1`、`ib_logfile0`构成了重做日志 _(崩溃恢复期间使用的基于磁盘的数据结构，用于纠正不完整事务写入的数据)_，除此之外，它们还记录试图更改 InnoDB 表中数据的语句。`ibdata1`另一方面，包含关于 InnoDB 表的元数据，以及一个或多个撤销日志、更改缓冲区和双写缓冲区的存储区域。

### 恢复原状

唯一值得庆幸的是，我设法备份了`/var/lib/mysql/database_name`的内容。这个目录保存了组成每个表的文件，它们是`*.frm`和`*.ibd`格式。还有一个单独的`db.opt`文件。`db.opt`文件存储了数据库的特征。`*.frm`文件是 MySQL 在磁盘上表示数据的方式。这些文件与扩展名为`*.frm`的表同名，这与使用的存储引擎无关。另一方面，`*.ibd`文件包含一个表及其相关的索引。

### 让修复开始。

为了解决这个问题，我不得不重新创建数据库模式——谢天谢地，我的 ORM 为我提供了一个非常好的迁移——并且我特别注意约束，尤其是外键。

作为恢复过程的一部分，我登录 MySQL 并禁用了外键约束:`SET FOREIGN_KEY_CHECKS=0;`，还丢弃了 InnoDB 表空间`ALTER TABLE table_name DISCARD TABLESPACE;`。我对所有新创建的 MySQL 表都这样做了。注意，默认情况下，InnoDB 将其表和索引存储在 system 表空间中。

> 系统表空间:包含 InnoDB 相关对象(InnoDB 数据字典)的元数据的一个或多个数据文件(ibdata 文件)，以及一个或多个撤消日志、更改缓冲区和双写缓冲区的存储区域。根据 innodb_file_per_table 设置，它还可能包含 innodb 表的表和索引数据。system 表空间中的数据和元数据适用于 MySQL 实例中的所有数据库。

*关于丢弃和导入 InnoDB 表空间的说明*

> 可以使用 DISCARD TABLESPACE 和 IMPORT TABLESPACE 选项来丢弃和导入在自己的逐表文件表空间中创建的 InnoDB 表。这些选项可用于从备份中导入每表一个文件的表空间，或将每表一个文件的表空间从一个数据库服务器复制到另一个数据库服务器。

在通过我的 ORM 完成数据库迁移并且我已经完成了所有表的`ALTER TABLE table_name DISCARD TABLESPACE`之后，我将所有的`*.frm`和`*.ibd`恢复到位于`/var/lib/mysql/database_name`的新创建的数据库目录中。

然后，我导入了之前丢弃的表空间:`ALTER TABLE table_name IMPORT TABLESPACE;`，与丢弃过程一样，我对所有表执行了导入操作。导入过程完成后，我恢复了外键约束`SET FOREIGN_KEY_CHECKS=1;`

### 我们完成了吗？

在这一点上，一切都应该是好的，周围应该是和平的，但没有，MySQL 有自己的思想。第一个问题是我遭受了索引损坏，在检查 MySQL 错误日志时，我发现了一个错误

```
[ERROR] InnoDB: Clustered record for sec rec not found 
InnoDB: index listings_slug_unique of table database.table_name 
InnoDB: sec index record PHYSICAL RECORD: n_fields 2; compact format; info bits 0 
```

Enter fullscreen mode Exit fullscreen mode

索引损坏非常罕见，通常是由 MySQL 错误或硬件故障引起的。损坏索引的解决方案是运行`OPTIMIZE TABLE table_name`。但是在大多数情况下，这还不够，你可能会以这样有趣的错误结束；

```
mysql> OPTIMIZE TABLE table_name;
+----------------+----------+----------+-------------------------------------------------------------------+
| Table | Op | Msg_type | Msg_text |
+----------------+----------+----------+-------------------------------------------------------------------+
| database.table_name | optimize | note | Table does not support optimize, doing recreate + analyze instead |
| database.table_name | optimize | error | Invalid default value for 'end_date' |
| database.table_name | optimize | status | Operation failed |
+----------------+----------+----------+—————————————————————————————————+ 
```

Enter fullscreen mode Exit fullscreen mode

这正是发生在我身上的事。仔细看看这个错误，第二行暗示了可能的问题`invalid default value for ‘end_date’`。我们的`end_date`列得到一个无效的时间戳。这是由于 MySQL 目前被设置为 *NO_ZERO_DATE* (严格)模式，我试图以这种格式`0000-00-00 00:00:00`给它提供时间戳。

解决这个问题的方法是将 SQL 模式设置为允许无效日期:`SET SQL_MODE='ALLOW_INVALID_DATES';`。这里的 SQL 模式阻止 MySQL 执行有效的日期检查。它检查以确保只有月份使用数字范围；1 到 12，day 也使用数字范围；1 到 31。请注意，它没有提供前导零— 01、02 等。

在这一点上，一切似乎都很顺利。最后，我重启了 MySQL，充满了喜悦和赞美:)

#### 参考文献

[MySQL :: MySQL 5.6 参考手册::14.7.4 InnoDB 每表文件表空间](https://dev.mysql.com/doc/refman/5.6/en/innodb-multiple-tablespaces.html) [MySQL InnoDB 丢失表但文件存在——超级用户](https://superuser.com/questions/675445/mysql-innodb-lost-tables-but-files-exist)

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/engineering/2017/05/16/mysql-rabbit-hole.html)T3】*