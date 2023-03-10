# SQL 中的位掩码

> 原文：<https://dev.to/booyaa/bitmasks-in-sql-55jc>

位掩码是表达谓词的一种非常方便的方式，不会因为 parens 和逻辑操作符(`AND`和`OR`)而变得过于冗长。假设我们有下表。

```
| what        | wanted |
|-------------|--------|
| need me too | 4      |
| alpha       | 1      |
| beta        | 2      |

with data as
(
select 'appears_in_both', 4 as wanted from dual
union all
select 'alpha', 1 as wanted from dual
union all
select 'beta', 2 as wanted from dual
)
... 
```

Enter fullscreen mode Exit fullscreen mode

## 把“太需要我了”和“阿尔法”联系在一起

```
...
select * 
from data
where bitand(wanted, 5) <> 0; -- (4 + 1) 
```

Enter fullscreen mode Exit fullscreen mode

## 把“太需要我了”和“贝塔”联系在一起

```
...
select * 
from data
where bitand(wanted, 6) <> 0; -- (4 + 2) 
```

Enter fullscreen mode Exit fullscreen mode