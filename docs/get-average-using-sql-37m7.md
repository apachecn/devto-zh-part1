# 使用 SQL 获取平均值

> 原文：<https://dev.to/adamkdean/get-average-using-sql-37m7>

今天又是 SQL，说起来我不是一个粉丝，我最近做了很多。

今天的片段很酷，我的灵感来自这个帖子:
[http://www . dream incode . net/forums/topic/190437-floating-point-division/](http://www.dreamincode.net/forums/topic/190437-floating-point-division/)

基本上，您通过合计表中每一项的价格来返回平均值，然后除以表中的行数。全部用 SQL。

```
SELECT (SELECT SUM(Price) FROM t_Items) /
    (SELECT COUNT(*) FROM t_Items)
        AS Expr1 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！