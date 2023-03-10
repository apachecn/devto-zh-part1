# 脏动态 SQL

> 原文：<https://dev.to/booyaa/dirty-dynamic-sql-36cb>

并不是所有的动态 sql 都严格要求立即执行，也不是肮脏的(我需要一个有趣的标题)。我从工作中的朋友那里学到了这个技巧，他非常相信使用 sql 来编码生成更多的 sql。

## 改变列长度

如果您试图收缩一个列，在运行生成的 sql 时，您仍然会得到一个错误。

```
SELECT 'ALTER TABLE ' || table_name || ' MODIFY ' || column_name || ' VARCHAR2(' || CASE column_name  WHEN 'FOO' THEN '10'  ELSE '20' END || ');' AS sql
  FROM user_tab_columns 
 WHERE column_name IN ( 'FOO','BAR')
   AND table_name IN (SELECT table_name FROM user_tables)   
   AND NOT regexp_like(table_name, '_(NEW|BAK)$') -- exclude backups 
```

Enter fullscreen mode Exit fullscreen mode

## 插入动态 sql 的输出

通常情况下，您会希望使用类似于`SELECT blah INTO v_foo`的东西，但是对于动态 sql，您需要将`INTO`放在`EXECUTE IMMEDIATE`语句中。

```
SET SERVEROUTPUT ON
DECLARE
  v_sql VARCHAR2(8000);
  v_count NUMBER;
  CURSOR c_tables
      IS
  SELECT table_name
    FROM user_tables;
BEGIN
  FOR rec in c_tables
  LOOP
    v_sql := 'SELECT count(*) FROM ' || rec.table_name;
    EXECUTE IMMEDIATE v_sql INTO v_count;

    DBMS_OUTPUT.put_line(rec.table_name || ' has ' || v_count);
  END LOOP;
END;
/ 
```

Enter fullscreen mode Exit fullscreen mode

## 使用替代变量

```
clear screen
set serveroutput on
declare
  cursor c_tables is select table_name from user_tables;
  v_data VARCHAR2(2000);
  v_sql VARCHAR2(2000);
  v_stuff VARCHAR2(50) := '&1'; -- try entering MAX(DATADATE) or COUNT(*) 
begin
  for rec in c_tables
  loop
    v_sql := 'SELECT TO_CHAR(' || v_stuff || ') FROM ' || rec.table_name;

    begin
      execute immediate v_sql into v_data;
      dbms_output.put_line(rec.table_name || ': ' || v_data);

    exception
      when others then
        dbms_output.put_line('failed to run: ' || v_sql);
        raise;
    end;
    end case;
  end loop;
end;
/ 
```

Enter fullscreen mode Exit fullscreen mode

## 一号护筒

### 赠款

```
SELECT 'GRANT ' || privilege || ' ON "' || GRANTOR ||'"."' || TABLE_NAME || 
       '" TO "' || GRANTEE || '";' 
FROM dba_tab_privs 
WHERE grantor = 'FOO'
AND grantee = 'BAR'
; 
```

Enter fullscreen mode Exit fullscreen mode

### 将同义词重新创建为 sys

```
select 'CREATE OR REPLACE SYNONYM "' || OWNER || '"."' || SYNONYM_NAME || 
       '" FOR "' || TABLE_OWNER || '"."' || TABLE_NAME || '";' 
from dba_synonyms 
where table_owner = 'FOO'; 
```

Enter fullscreen mode Exit fullscreen mode