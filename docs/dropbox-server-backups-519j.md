# Dropbox 服务器备份

> 原文：<https://dev.to/scottrobertson/dropbox-server-backups-519j>

在过去的几个月里，我一直在为我的副总裁们寻找备份解决方案。我想要一个单一的解决方案，允许我备份多个源，如 MongoDB、MySQL 和常用文件。

经过一段时间的搜索，我决定我会建立自己的，由于无法找到完美的解决方案。

在此之前，我只是在每台机器上运行 Dropbox 服务，并有一个 cron 将 mysqldump 复制到 Dropbox 文件夹中，并进行同步。这工作得很好，但是设置起来很费力。

因此，我选择直接使用 Dropbox API 从工具中备份。这允许我将脚本部署到任何服务器上([见这里](https://blog.scottrobertson.me/post/git-deployments))，唯一的依赖是 PHP 和 Curl。

设置时，您只需运行:

```
bin/console dropbox:auth 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供一个 Dropbox 的授权链接，它将返回一个用于构建您的配置文件的令牌。

设置好配置文件后(详见 repo 的 README.md)。您将能够简单地设置一个 cron 来运行下面的实例:

```
bin/console export:mysql 
```

Enter fullscreen mode Exit fullscreen mode

这将循环遍历你可以访问的每个数据库，mysqldump，gzip 并上传到 Dropbox。我的 MySQL(和 MongoDB)备份设置为每小时备份一次，但是文件存储在 YMD，这意味着文件将被覆盖。这通常是一件坏事，因为你可以很容易地用坏的备份覆盖，但因为我们使用 Dropbox，它保留了内部版本，这意味着我们每天复制大约 23 份，但我们将存储空间保持在最低水平。

如果你想查看源代码，并可能使用该工具，那么请前往 [Github](http://github.com/scottrobertson/backup) 。如果你有任何问题或建议，请告诉我。