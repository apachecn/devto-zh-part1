# 在纯 MySQL 中实现软删除

> 原文：<https://dev.to/domysee/implementing-soft-delete-in-pure-mysql-40ke>

软删除，设置一个删除标志，而不是实际删除，是一个伟大的方式，以确保能够恢复用户意外删除数据的许多应用程序。有些技术，比如 Ruby on Rails，内置了软删除功能，所以开发者不需要做任何事情就可以使用它。

可惜，别人没有那个，而且对某些人来说整合起来相当困难。我用实体框架 6 试过。它起作用了，但是远没有我希望的那么干净。

因此，对于在应用程序中添加软删除需要太多工作的情况，我开发了一个 MySQL 脚本，将它添加到数据库中，而根本不用改变应用程序。

## 脚本

该脚本是一个存储过程，它遍历所有表，并创建向数据库添加软删除所需的 SQL 语句。

```
CREATE DEFINER=`root`@`localhost` PROCEDURE `AddSoftDelete`()
BEGIN
    DECLARE q, result TEXT;
    DECLARE tableName TEXT;
    DECLARE columnList TEXT;

    DECLARE done1 INT DEFAULT FALSE;
    DECLARE tableNamesCursor CURSOR FOR 
    SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_SCHEMA = "your_schema";
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done1 = TRUE;

    SET result = "";
    OPEN tableNamesCursor;
    tableLoop: LOOP
        FETCH tableNamesCursor INTO tableName;
        IF done1 THEN 
            LEAVE tableLoop;
        END IF;

        SET q = CONCAT("CREATE TABLE ", tableName, "_deleted SELECT * FROM ", tableName, " WHERE 1 = 0;");  #don't copy data and don't copy indexes
        SET result = CONCAT(result, q, "\n");

        SET q = CONCAT("ALTER TABLE ", tableName, "_deleted ADD Deleted DATETIME;");
        SET result = CONCAT(result, q, "\n");

        #Create Triggers to move removed Data
        SET result = CONCAT(result, "DELIMITER |", "\n");

        SET columnList = "";
        BLOCK2: BEGIN
            DECLARE columnName, columnTableName TEXT;
            DECLARE done2 INT DEFAULT FALSE;
            DECLARE columnNamesCursor CURSOR FOR 
            SELECT COLUMN_NAME, TABLE_NAME FROM information_schema.COLUMNS WHERE TABLE_SCHEMA = "schema_name";
            DECLARE CONTINUE HANDLER FOR NOT FOUND SET done2 = TRUE;

            OPEN columnNamesCursor;
            columnLoop: LOOP
                FETCH columnNamesCursor INTO columnName, columnTableName;
                IF done2 THEN 
                    LEAVE columnLoop;
                END IF;

                IF columnTableName = tableName THEN
                    IF columnList = "" THEN
                        SET columnList = CONCAT("OLD.", columnName);
                    ELSE
                        SET columnList = CONCAT(columnList, ", OLD.", columnName);
                    END IF;
                END IF;
            END LOOP columnLoop;
        END BLOCK2;

        SET q = CONCAT("CREATE TRIGGER ", tableName, "_delete AFTER DELETE ON ", tableName, " FOR EACH ROW BEGIN INSERT INTO ", tableName, "_deleted VALUES(", columnList, ", NOW()); END |"); #NOW() for the Deleted Column
        SET result = CONCAT(result, q, "\n");

        SET result = CONCAT(result, "DELIMITER ;", "\n");

        SET result = CONCAT(result, "\n");
    END LOOP tableLoop;

    SELECT result;
END 
```

添加存储过程后，您可以用
调用它

```
call AddSoftDelete(); 
```

然后你会得到输出并执行它。对于数据库中的每个表，它创建一个添加了后缀“_deleted”的表，该表与原始表具有相同的结构，在原始表上添加一个“deleted”列和一个触发器，该触发器将删除的列插入到“_deleted”表中。创建的表不会有任何索引。这是为了确保不会因重复条目而出现错误。因为原始表中的行实际上被删除了，所以有可能有多个 id 相同的行，只是不在同一时间。但是所有这些行总有一天会出现在“_deleted”表中，所以它不应该检查重复数据。

下面是我的“people”表的输出示例(每个表都有一个这样的语句块):

```
CREATE TABLE people_deleted SELECT * FROM people WHERE 1 = 0;
ALTER TABLE people_deleted ADD Deleted DATETIME;
DELIMITER |
CREATE TRIGGER people_delete AFTER DELETE ON people FOR EACH ROW BEGIN INSERT INTO people_deleted VALUES(OLD.Id, OLD.Firstname, OLD.Lastname, OLD.Sex, OLD.SocialSecurityNumber, OLD.DateOfBirth, NOW()); END |
DELIMITER ; 
```

所以这就是全部了。希望对你有帮助！

*“但是如果我想再摆脱它呢”*你问？*“想必你也为此写过剧本吧”*。答案是肯定的，我确实这样做了。我当然会为你提供。
T5 就是这里:

```
CREATE PROCEDURE `RemoveSoftDelete`()
BEGIN
    DECLARE q, result TEXT;
    DECLARE tableName TEXT;
    DECLARE done INT DEFAULT FALSE;
    DECLARE tableNamesCursor CURSOR FOR 
    SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_SCHEMA = "your_schema";
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

    SET result = "";
    OPEN tableNamesCursor;
    tableLoop: LOOP
        FETCH tableNamesCursor INTO tableName;
        IF done THEN 
            LEAVE tableLoop;
        END IF;

        IF REPLACE(tableName, "_deleted", "") != tableName THEN
            SET q = CONCAT("DROP TABLE ", tableName, ";");
            SET result = CONCAT(result, q, "\n");
        END IF;

        IF REPLACE(tableName, "_deleted", "") = tableName THEN            
            SET q = CONCAT("DROP TRIGGER ", tableName, "_delete;");
            SET result = CONCAT(result, q, "\n");
        END IF;
    END LOOP;

    SELECT result;
END 
```

它创建 SQL 语句来删除添加的表和触发器。下面是我的“people”表的输出示例:

```
DROP TRIGGER people_delete;
DROP TABLE people_deleted; 
```

现在真的是这样了。快乐编码:)

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。