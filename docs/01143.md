# 在 MySQL(InnoDB )中将 charset 设为 utf8mb4 的注意事项的当前

> 原文：<https://dev.to/seizans/mysqlinnodb--charset--utf8mb4--1451>

出于想支持图画文字等理由而使用 utf8mb4。
但是，以前存在只设定为 utf8mb4 的问题。
MySQL 5.7.9 以后已经解决，所以总结其内容。

## 字符集を utf8mb4 にする

只要不在服务器整体上设定即可，在 my.cnf 上设定以下内容即可。

```
[mysqld]
character_set_server=utf8mb4
collation_server=utf8mb4_unicode_ci 
```

Enter fullscreen mode Exit fullscreen mode

如果想以 database 为单位进行设定的话，发行下面的就可以了。

```
ALTER DATABASE spam CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci; 
```

Enter fullscreen mode Exit fullscreen mode

## 应该应对的问题

如果改为 utf8mb4，在 VARCHAR 等列中，即使是相同的字符数，size 也会变大。
默认情况下，该 size 的上限为 767byte，会试图扩展索引而掉落等。
在[MySQL(InnoDB )中被称为" index column size too large.the maximum column size is 767 bytes."时的对策](http://blog.kamipo.net/entry/2012/11/13/102024)中详细叙述

## 以前的解决方法

通过使 innodb_large_prefix 参数为 ON，size 上限变为 3072 字节，从而没有问题。
但是，为此，需要将表的 ROW_FORMAT 设为 DYNAMIC (或 COMPRESSED )。
并且为此，innodb_file_format 需要是 Barracuda。

所以实际上是

```
innodb_large_prefix=ON
innodb_file_format=Barracuda
innodb_file_format_max=Barracuda 
```

Enter fullscreen mode Exit fullscreen mode

作为，另外在创建表时加上`ROW_FORMAT=DYNAMIC`就解决了。

## Now

*   `innodb_file_format`默认为`Barracuda`(5.7.7 以上)，
*   `innodb_default_row_format`默认为`DYNAMIC`(5.7.9 以上)
*   `innodb_large_prefix`缺省为 ON (5.7.7 或更高版本)，在 deprecated 中，预计同一个项目消失

因此，为了解决上述问题，应该设定的项目在 5.7.9 以后就没有了，已经解决了。

## Reference

*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-parameters . html # sys var _ innodb _ file _ format](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_format)
*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-parameters . html # sys var _ innodb _ default _ row _ format](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_default_row_format)
*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-parameters . html # sys var _ innodb _ large _ prefix](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_large_prefix)
*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-restrictions . html](https://dev.mysql.com/doc/refman/5.7/en/innodb-restrictions.html)
*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-row-format-specification . html](https://dev.mysql.com/doc/refman/5.7/en/innodb-row-format-specification.html)
*   [https://dev . MySQL . com/doc/ref man/5.7/en/innodb-row-format-dynamic . html](https://dev.mysql.com/doc/refman/5.7/en/innodb-row-format-dynamic.html)