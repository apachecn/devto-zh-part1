# 绑定与替代变量

> 原文：<https://dev.to/booyaa/bind-vs-substitution-variables-58h>

我总是很难记住这些类型的变量之间的区别。虽然现在，我已经开始做了很多与 ORDS 相关的工作，这种差异变得更加明显。

另外，替代变量实际上是对用户变量的绑定。

## 绑定变量

```
PROMPT bind variables are...
VAR foo VARCHAR2

SET SERVEROUTPUT ON
BEGIN
  :FOO := 'in PL/SQL blocks';
  DBMS_OUTPUT.PUT_LINE('mostly used...');
END;
/
SET SERVEROUTPUT OFF

PRINT foo 
```

Enter fullscreen mode Exit fullscreen mode

输出

```
bind variables are...

PL/SQL procedure successfully completed.

mostly used...

FOO
--------
in PL/SQL blocks 
```

Enter fullscreen mode Exit fullscreen mode

## 替代变量

```
PROMPT Where as substitution variable are...
DEFINE FOO = 'useful in SQL scripts'
SET VERIFY OFF
SELECT '&FOO' AS BAR FROM DUAL;
SET VERIFY ON 
```

Enter fullscreen mode Exit fullscreen mode

输出

```
Where as user variable are...

BAR                 
--------------------------
useful in SQL scripts 
```

Enter fullscreen mode Exit fullscreen mode

明确地说，使用绑定变量与 PL/SQL 块交互，替代变量可以在任何地方使用。替代变量的唯一缺点是不能用另一个用户变量定义一个变量。

你也可以打破替代变量，如果有一个错误的`SET DEFINE OFF`，仍然不知道如何测试这个？可能使用默认值并对其进行测试，例如 SQLCMD 变量。

延伸阅读:

*   [文字、替代变量和绑定变量](https://oracle-base.com/articles/misc/literals-substitution-variables-and-bind-variables)
*   [PL/SQL 101:替换与绑定变量](https://community.oracle.com/docs/DOC-915518)