# 通过预写日志记录(WAL)加速 SQLite

> 原文：<https://dev.to/lefebvre/speed-up-sqlite-with-write-ahead-logging-wal-do>

当您在一个事务中更新一个 [SQLite](https://www.sqlite.org) 数据库时，标准行为是将原始数据复制到一个单独的回滚文件中，并将新数据直接写入 DB 文件中。这是可靠的，但它会导致每次数据库更改都要进行两次磁盘写入。提交时，回滚文件将被删除，因为不再需要它。如果改为回滚，则数据将从回滚文件恢复到数据库文件。

为了让 SQLite 更快，您可以启用一个名为预写日志(又名 WAL)的特性。WAL 可以更快，因为它逆转了上述行为。使用 WAL，不是修改 DB 文件本身并维护一个单独的回滚文件，而是将对 SQLite 数据库的每个更改写入一个单独的“预写日志文件”(通常以“-wal”结尾)。这导致只有一次磁盘写入。

此外，由于文件是独立的，多线程应用程序能够在写入数据库的同时从数据库中读取数据。反之亦然。对于需要数据库，但不需要全功能数据库服务器的 web 应用程序来说，这是一个特别好的好处。

要启用 WAL，您可以使用 SQLite Pragma 命令:

```
PRAGMA journal_mode=WAL; 
```

Enter fullscreen mode Exit fullscreen mode

在 [Xojo](http://www.xojo.com) 代码中，您使用 SQLExecute 方法向 SQLite DB 发送 Pragma 命令:

```
DB.SQLExecute("PRAGMA journal_mode=WAL;") 
```

Enter fullscreen mode Exit fullscreen mode

或者更简单地说，您可以设置 SQLiteDatabase。多用户属性为 True:

```
DB.MultiUser = True 
```

Enter fullscreen mode Exit fullscreen mode

单独的 WAL 文件中的数据在某个时候必须被传输回原始数据库。这被称为“检查点”。您可以手动执行这些操作，或者让 SQLite 自动处理它们，根据 SQLite docs 的说法，当 WAL 文件达到 1000 页的阈值时，就会发生这种情况。

您可以使用 wal_checkpoint Pragma 命令手动启动检查点:

```
PRAGMA schema.wal_checkpoint; 
```

Enter fullscreen mode Exit fullscreen mode

在 Xojo 代码中，可以使用 SQLExecute 方法将这个 Pragma 命令发送到 DB:

```
DB.SQLExecute("PRAGMA schema.wal_checkpoint;") 
```

Enter fullscreen mode Exit fullscreen mode

使用 WAL 也有不好的一面。当有一个大的 WAL 文件时，读取性能会受到影响，因为必须在两个地方搜索数据。WAL 上的官方 SQLite 文档也列出了一些其他的缺点。

但是对于大多数类型的使用，WAL 是值得使用的。在你的应用中尝试一下，看看它们是否能从中获益。

*本文原文出现在这里: [SQLite 预写日志](http://developer.xojo.com/userguide/sqlite-write-ahead-logging)T3】*