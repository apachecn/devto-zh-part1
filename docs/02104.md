# 使用 PostgreSQL 的 7 个理由

> 原文：<https://dev.to/lefebvre/7-reasons-you-should-be-using-postgresql-1d0>

PostgreSQL 是一个开源的对象关系数据库系统，已经有超过 15 年的积极发展。以下是你应该使用 PostgreSQL 的 7 个理由。

# 自由

PostgreSQL 完全免费使用。它有一个类似于 BSD 和 MIT 的许可证，这意味着你可以不受任何限制地使用它，不像 MySQL 等其他开源数据库。

# 易于设置

想在 macOS 上测试 PostgreSQL？您可以使用 [Postgres.app](http://postgresapp.com/) 来获得一个工作的 PostgreSQL 数据库，只需运行一个应用程序，根本不需要安装。

即使你需要安装 PostgreSQL，简单的安装程序也可以通过预构建的二进制文件用于 Windows、macOS 和 Linux。

如果你需要更多的支持，你可以求助于 EnterpriseDB，它为 PostgreSQL 提供支持。

通常，您甚至不必费心安装 PostgreSQL，因为许多服务器主机会自动提供它作为一个选项，您可以轻松地打开或配置它。例如，使用 [Xojo Cloud](http://www.xojo.com/cloud) ，只需点击一下它的[控制面板](http://developer.xojo.com/xojo-cloud-control-panel)就可以启用 PostgreSQL。

在其他托管公司，你可能必须自己安装 PostgreSQL，但通常会提供步骤。例如，Digital Ocean 提供了在 CentOS 7 中安装 PostgreSQL 的这些[步骤](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-7)。

# 强大

PostgreSQL 拥有您在服务器数据库中期望的所有专业级特性。您可以拥有多个用户、多个数据库、存储过程、表的健壮类型、视图、完整 SQL、备份等等。

# 安全

PostgreSQL 有用于安全连接的 SSL。它会定期更新，并被许多组织使用，以确保根据需要更新安全更新。有关这方面的更多信息，请参考完整的 [PostgreSQL 安全报告](http://www.postgresql.org/support/security/)。

# 灵活

您可以在任何编程语言中使用 PostgreSQL。因为 PostgreSQL 无处不在，几乎任何编程语言都可以轻松地连接到它。在 Xojo 中，您的代码如下所示:

```
Dim pg As New PostgreSQLDatabase
pg.Host = “sheep”
pg.DatabaseName = “Mary”
pg.Port = 5432
pg.UserName = “lamb”
pg.Password = “foo”
If pg.Connect Then
  // Connected
End If 
```

Enter fullscreen mode Exit fullscreen mode

在 PHP 中，您的代码如下所示:

```
$dbconn3 = pg_connect("host=sheep port=5432 dbname=mary user=lamb password=foo"); 
```

Enter fullscreen mode Exit fullscreen mode

# 
  
大量工具

使用 GUI 工具时，使用 PostgreSQL 要容易得多。有很多很多可用的工具，比如 pgAdmin、Navicat 和 SQLPro。有关完整列表，请参考 [PostgreSQL 工具 wiki 页面](https://wiki.postgresql.org/wiki/Community_Guide_to_PostgreSQL_GUI_Tools)。

# 文档

如果您在使用 PostgreSQL 时被难住了，您会发现有很多信息和文档可用。和往常一样，[官方文档](http://www.postgresql.org/docs/)是一个很好的起点。

还有几十本关于如何使用 PostgreSQL 的书籍和信息，你可以很容易地找到，其中许多都列在官方文档页面的[中。](http://www.postgresql.org/docs/)

想要了解更多信息吗？观看这段 PostgreSQL 视频:

[https://www.youtube.com/embed/3zNeePbHn48](https://www.youtube.com/embed/3zNeePbHn48)

正如 PostgreSQL 网站所说，它是“世界上最先进的开源数据库”。一旦你尝试了，你肯定会同意的！

这篇文章最初出现在 Xojo 的博客上