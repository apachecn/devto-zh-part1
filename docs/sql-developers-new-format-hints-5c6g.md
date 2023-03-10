# SQL Developer 的新格式提示

> 原文：<https://dev.to/booyaa/sql-developers-new-format-hints-5c6g>

SQL Developer 中我最喜欢的一个很酷的特性是能够将 SQL 查询输出转换成完全不同的格式。它甚至不需要打开导入/导出向导。

```
SPOOL C:\TEMP\foobar.csv

SELECT /*csv*/ *
  FROM foo
  LEFT
  JOIN bar
    ON foo.id = bar.id
 WHERE foo.name LIKE '%HURR%';

 SPOOL OFF 
```

Enter fullscreen mode Exit fullscreen mode

格式的完整列表

```
SELECT /*csv*/ * FROM scott.emp;
SELECT /*xml*/ * FROM scott.emp;
SELECT /*html*/ * FROM scott.emp;
SELECT /*delimited*/ * FROM scott.emp;
SELECT /*insert*/ * FROM scott.emp;
SELECT /*loader*/ * FROM scott.emp;
SELECT /*fixed*/ * FROM scott.emp;
SELECT /*text*/ * FROM scott.emp; 
```

Enter fullscreen mode Exit fullscreen mode

完整的细节和来源可以[这里](http://www.thatjeffsmith.com/archive/2012/05/formatting-query-results-to-csv-in-oracle-sql-developer/)。