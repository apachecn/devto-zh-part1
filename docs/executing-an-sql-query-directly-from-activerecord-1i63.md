# 直接从 ActiveRecord 执行 SQL 查询

> 原文：<https://dev.to/rendegosling/executing-an-sql-query-directly-from-activerecord-1i63>

您可以直接从 ActiveRecord 执行 sql 查询

### #执行

```
users = ActiveRecord::Base.connection.execute("SELECT id, first_name, created_at FROM users WHERE id=1")

users[0]
{
  "id"         => "1",
  "first_name" => "Ren",
  "created_at" => "2017-01-01 12:23:67.425118"
} 
```

Enter fullscreen mode Exit fullscreen mode