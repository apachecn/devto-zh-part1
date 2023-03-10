# 正在为 Docker 安装 SQL Server

> 原文：<https://dev.to/prestonjlamb/installing-sql-server-for-docker-33np>

SQL Server 是一个强大的数据库选项，但直到最近，除非您在 Windows 机器上，否则不可能使用它。嗯，这种情况已经改变了(和许多其他微软产品一起)。本周在工作中，我决定最终使用 Docker 让 SQL Server 在我的 Mac 上运行。以下是我如何做到这一点的快速回顾。

我曾经有三个页面可以运行。第一个是在 [Docker Hub](https://hub.docker.com/r/microsoft/mssql-server-linux/) 的图像信息页面上。只需按照那里的简单说明，您就可以获取图像并运行容器。

现在，如果您不需要将现有的数据库备份到您的实例中，您就万事俱备了！使用 [VS 代码和 mssql 扩展](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-develop-use-vscode)连接到数据库，或者任何其他在 Mac 上运行并连接到 SQL Server 的数据库管理应用程序。但是，在我的例子中，我需要恢复一个我们工作中使用的数据库，这样我就可以在本地保存这些数据。为此需要接下来的两步。

当我寻找如何做到这一点的指导时，我遇到了这个关于堆栈溢出的答案。我个人在他的回答中没有使用 step 3，因为我是用 VS 代码和 mssql 连接数据库的。基本上，他的回答是让你获得容器的 ID，将备份文件从你的计算机复制到容器中，然后在 SQL Server 中运行一个`RESTORE DATABASE FROM FILE`命令。

对我来说，这还不足以让我回家。我有一个错误，数据库说它找不到备份中引用的 MDF 和 LDF 文件。这就是这篇博客文章帮助解决的地方。帖子说只需指定新的 MDF 和 LDF 文件应该在容器中的什么位置。这很简单，而且很快就见效了。

最后，下面是我运行的完整命令:

```
RESTORE DATABASE [MyDatabase]
FROM DISK = N'/var/opt/mssql/backup.bak'
WITH FILE = 1, NOUNLOAD, REPLACE, STATS = 5,
MOVE 'MyDatabase' TO '/var/opt/mssql/data/MyDatabase.mdf',
MOVE 'MyDatabase_log' TO '/var/opt/mssql/data/MyDatabase_log.ldf' 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，这是一个易于启动和运行的项目。找到合适的资源需要一段时间，这就是为什么我决定把它们放在一个地方，但是使用 Docker 在 Mac 或 Linux 上安装并运行 SQL Server 应该不超过 20-30 分钟。