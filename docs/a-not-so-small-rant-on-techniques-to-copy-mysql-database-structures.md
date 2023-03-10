# 关于复制 MySQL 数据库结构的技术的一个(不那么)小的咆哮

> 原文：<https://dev.to/igorsantos07/a-not-so-small-rant-on-techniques-to-copy-mysql-database-structures>

我在这里，面临一个简单的任务，将原始公司的数据库复制到一个测试数据库中，这样我们就可以，嗯，运行测试。

你可能会猜测，这只是一个问题。
一些罕见的人甚至会提到`CREATE TABLE x2 LIKE x1`。非常聪明。

但是 MySQL 不适合你。最后，他是一个相当值得尊敬但也有缺点的家伙。我写这篇小文章，总结我在这次冒险中找到的所有资源，希望能帮助其他冒险者。

> TL；DR:使用 mysqldump 和 SSH，或者使用字符串魔法

## `CREATE TABLE LIKE`不抄观点

这个问题很简单:显然，您不能用`CREATE TABLE`命令创建视图。
不，没有`CREATE VIEW x2 LIKE x1`，因为视图的用法只在某些情况下类似于表格。你不得不求助于弦乐魔法和`SHOW CREATE VIEW`的输出。

> 额外收获:我应该提到没有办法在像`SHOW TABLES`这样的命令上将表格和视图分开吗？

## `CREATE TABLE LIKE`从结构中复制一切，约束除外

[手册告诉您](https://dev.mysql.com/doc/refman/5.6/en/create-table-like.html)您可以使用该命令复制您的表，包括列属性和索引。但是没有明确的声明说你的[约束会被遗忘](http://stackoverflow.com/questions/3755591/mysql-copy-table-structure-including-foreign-keys)。
它还告诉你[外键将被`mysqldump`正确地](https://dev.mysql.com/doc/refman/5.6/en/create-table-foreign-keys.html#idm140024646591168)转储，甚至告诉你实用程序的排序问题。但是为什么要告诉你负面的、未解决的问题，比如那些约束没有包含在`CREATE TABLE LIKE`复制操作中？

所以在这两种情况下，你不得不求助于弦乐魔法。MySQL 为此构建的常用特性不起作用。

## `mysqldump`可能要花一辈子才能跑过一个网络

在某些情况下，我们需要将结构从一台机器复制到另一台机器上，所以最好的方法是简单地给 mysqldump 主机、用户和密码，并要求他完全转储数据库模式，对吗？

哦，好吧，如果我们进入服务器并运行 mysqldump，只需要 3 秒钟就可以转储所有内容。通过 SSH 下载这个转储文件也需要同样长的时间。
如果使用主机/用户/密码[完成，可能需要大约 3 分钟](http://dba.stackexchange.com/questions/165055/mysqldump-takes-forever-to-dump-tables-from-a-remote-server)(!！！)，通过完全相同的 VPN 网络。

因此，我们必须在应用程序中填充一些 SSH 凭证。就为了那一次手术。DB 凭证对于 MySQL 的转储协议是不够的。去想吧。

## `mysqldump`可能会忽略你的外键顺序

同样，如手册中所述， [`mysqldump`将禁用转储中的外键检查](https://dev.mysql.com/doc/refman/5.6/en/create-table-foreign-keys.html#idm140024646591168)，因此在导入过程中可以忽略表顺序。
然而，这取决于你的命令行选项。如果你太天真，试图加速你的转储，MySQL 将会取消你的禁用命令。您必须手动添加它——这并不难，因为您无论如何都要在转储中重命名原始数据库……

## mysqldump 上的视图或 SHOW CREATE 视图包含与原始用户相关的信息

在你的转储中，还有一件事你可以得到`replace()`:视图部分将包括算法和定义者选项——最后一个，指向创建它的用户。除非该用户是您自己，否则您必须替换它，否则 MySQL 会要求您提供比您更多的权限。

```
CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`192.168.0.1` SQL SECURITY DEFINER VIEW `mydb`.`v_cool_view` AS (…) 
```

Enter fullscreen mode Exit fullscreen mode

## 最终解决

为了让东西在本地和整个网络上都能工作，我们必须:

### 验证我们是否有原始数据库的本地副本。如果答案是肯定的…

1.  为每个表和视图获取 CREATE 语句
2.  更改这些命令所需的信息(尤其是创建视图命令)
3.  运行删除和外键检查
4.  运行每个创建命令

> 事实证明，处理`SHOW CREATE`语句花费的时间和`CREATE TABLE LIKE`花费的时间一样多，甚至更少。

### 如果我们必须连接到外部数据库…

1.  在那台机器上运行 mysqldump over ssh
2.  根据需要更改转储，尤其是在查看命令时
3.  添加删除和外键检查
4.  将它写入一个临时文件，并在 mysql 客户端上运行它，因为我们必须在运行它之前改变转储，简单的管道是不行的；用 cat 和 sed 修改它会使事情变得太复杂，而且没有办法通过我们的 ORM 一次运行一堆查询，也没有可靠的方法在转储文件中分离每个查询(许多查询是注释隔离的)。

* * *

> ### Sir, good luck on your MySQL trip. Good luck to you.