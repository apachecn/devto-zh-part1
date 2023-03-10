# 随机甲骨文提示

> 原文：<https://dev.to/booyaa/randpm-oracle-tips-3kh8>

## 隐藏用户输入

有时候你需要保守一些秘密(肩膀冲浪)，这只会在 SQL/Plus 或者 Oracle SQL Developer 中起作用(F5/Run script 模式又名 broken SQL/Plus 模式)。

```
SET SERVEROUTPUT ON

SET VERIFY OFF

ACCEPT sekrit PROMPT 'enter a secret (warning we''re going to print it on screen!)' HIDE

BEGIN
    dbms_output.put_line('sekrit: ' || '&sekrit');
END;
/ 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行脚本正确，输入对话将回显星星`*`而不是你的“秘密”。

如果您可以看到正在输入的数据，则说明您运行了 Oracle SQL Developer 语句模式(CTRL-ENTER)。

## 会话

### 寻找

```
SET LINESIZE 140
SET PAGESIZE 50
COL sid FORMAT a5
COL serial FORMAT a5
COL username FORMAT a30
COL osuser FORMAT a30
COL machine FORMAT a30
COL client_ip FORMAT a20
SELECT
    sid,
    serial# AS serial,
    osuser,
    username,
    machine,
    logon_time,
    utl_inaddr.get_host_address(regexp_replace(machine,'^.+\\') ) AS client_ip
FROM
    v$session
WHERE
        username IS NOT NULL
    AND
        status <> 'KILLED'; 
```

Enter fullscreen mode Exit fullscreen mode

### 杀戮

`alter system kill session 'sid,serial#';`

代码生成示例

```
SELECT 
    'alter system kill session ''' ||sid||','||serial#|| ''';' as sql
FROM
    v$session
WHERE
    username = 'VICTIM'; 
```

Enter fullscreen mode Exit fullscreen mode