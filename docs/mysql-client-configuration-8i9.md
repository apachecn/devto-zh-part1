# MySQL 客户端配置

> 原文：<https://dev.to/michaelmior/mysql-client-configuration-8i9>

也许我是唯一一个不知道这一点的人，但是不久前，我发现了一个配置 MySQL 客户端的便利技巧。您可以在您的主目录`~/.my.cnf`中创建一个配置文件，并设置 MySQL 客户端要使用的选项。如果您经常连接到同一个服务器，并且厌倦了输入主机名和端口参数，这将非常方便。

这里有一个例子:

```
[client]
host = 127.0.0.1
user = root
password = root
port = 4040 
```

Enter fullscreen mode Exit fullscreen mode

这个帖子到此为止。如果你知道任何其他节省时间的 MySQL 技巧，请在评论中分享！