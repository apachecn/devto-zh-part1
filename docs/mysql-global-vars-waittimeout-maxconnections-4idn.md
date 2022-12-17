# MySQL 全局变量 wait_timeout & max_connections

> 原文：<https://dev.to/adamkdean/mysql-global-vars-waittimeout-maxconnections-4idn>

只是一些代码让我收藏。问题是 MySQL 连接没有被关闭，超时设置为 8 小时，连接限制为 400。

```
SHOW VARIABLES LIKE "%wait%"
SET @@GLOBAL.wait_timeout=300

SHOW VARIABLES LIKE "%max%"
SET @@GLOBAL.max_connections=1000 
```

Enter fullscreen mode Exit fullscreen mode