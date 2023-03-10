# MySQL 用户和权限

> 原文：<https://dev.to/emmanuelgautier/mysql-users-permissions-3j6>

数据库管理包括用户和权限管理。大多数时候，类似于 [MySQL Workbench](https://www.mysql.fr/products/workbench/) 或 [PHPMyAdmin](https://www.phpmyadmin.net/) 的 UI 可用于执行用户管理操作。在这里，我们将看到如何使用 SQL 查询进行一些 MySQL 用户和权限管理。

### 用户创建

第一项操作，使用密码创建用户。执行此操作的两种方式取决于您希望传递密码的方式。

```
-- Not hashed password in the query (not very secure)
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';

-- With hashed password
SELECT PASSWORD('password'); -- Création du Hash du mot de passe
CREATE USER 'user'@'localhost' IDENTIFIED BY PASSWORD '*2470C0C06DEE42FD1618BB9900DFG1E6Y89F4'; 
```

Enter fullscreen mode Exit fullscreen mode

### 用户重命名

现在，让我们看看如何使用下面的查询重命名已经创建的用户。

```
RENAME USER 'user'@'localhost' TO 'user2'@'localhost'; 
```

Enter fullscreen mode Exit fullscreen mode