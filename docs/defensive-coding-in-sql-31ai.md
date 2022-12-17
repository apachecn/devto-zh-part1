# SQL 中的防御性编码

> 原文：<https://dev.to/booyaa/defensive-coding-in-sql-31ai>

为了避免谓词被意外删除，请始终在子句中换行。如果删除 AND 子句，下面的代码会发出尖叫。

```
SELECT *
  FROM foo
  LEFT JOIN bar
    ON ((foo.id = bar.id)
        AND (foo.fizz = bar.buzz)) 
```

Enter fullscreen mode Exit fullscreen mode

哪里像下面不会。

```
SELECT *
  FROM foo
  LEFT JOIN bar
    ON foo.id = bar.id
        AND foo.fizz = bar.buzz 
```

Enter fullscreen mode Exit fullscreen mode