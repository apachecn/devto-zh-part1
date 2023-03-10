# 使用 PDO 的数据库级 CRUD 操作

> 原文：<https://dev.to/rizwan_saquib/database-level-crud-operations-using-pdo>

没有数据库，任何真实的 PHP 应用程序都无法运行。

这就是为什么 PHP 提供了三种操作数据库和与数据库交互的强大方法。名单由 *MySQL* 、*MySQL*和 *PDO* 组成。

为了消除由这些不同的数据库管理方法引起的所有问题，PHP 已经标准化了 PHP 数据对象(PDO)作为数据库层的选择。PDO 为与 PHP 应用程序中的数据库交互提供了简单且非常精简的接口。要使用 PDO，必须安装数据库特定的驱动程序。

# 支持的数据库

PDO 通过特定的驱动程序支持所有流行的数据库。实际上，下列数据库驱动程序在 PDO 是可用的。

*   Cubrid (PDO_CUBRID)
*   FreeTDS /微软 SQL Server / Sybase (PDO 数据库)
*   火鸟(PDO _ 火鸟)
*   IBM DB2(PDO IBM)
*   IBM Informix 动态服务器(PDO Informix)
*   MySQL 3.x/4.x/5.x (PDO_MYSQL)
*   Oracle 呼叫接口(OCI PDO)
*   ODBC v3 (IBM DB2、unixODBC 和 win32 ODBC) (PDO_ODBC)
*   PostgreSQL (PDO
*   SQLite 3 和 SQLite 2 (PDO_SQLITE)
*   微软 SQL Server / SQL Azure (PDO_SQLSRV)
*   4D(4D PDO)。

默认情况下，PHP 安装了 PDO_SQLite 驱动程序。但是，如果您希望使用其他数据库，[您必须首先安装相关的驱动程序](http://php.net/manual/en/pdo.installation.php)。

为了检查您的系统上安装了哪些驱动程序，创建一个新的 PHP 文件，并向其中添加以下代码:

```
<?php
print_r(PDO::getAvailableDrivers());
?> 
```

Enter fullscreen mode Exit fullscreen mode

# 通过 PDO 执行 CRUD 操作

PDO 是在 PHP 中执行 CRUD 和相关 DBMS 操作的最佳方式，因为 PDO 标准化了可重用的代码，不会产生重大问题。实际上，PDO 充当了一个抽象层，将数据库相关操作与代码的其余部分分离开来。

## 数据库连接

PDO 的连接很简单。考虑以下用于建立与数据库的连接的代码片段。请注意，当底层数据库发生变化时，您需要在这段代码中进行的唯一更改是数据库类型。

```
 <?php

Class Connection {

private  $server = "mysql:host=localhost;dbname=cloudways";
private  $user = "root";
private  $pass = "";
private $options  = array(PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,);
protected $con;
       public function openConnection()
       {
            try
            {
            $this->con = new PDO($this->server, $this->user,$this->pass,$this->options);
            return $this->con;
             }

catch (PDOException $e)
             {
                                                echo "There is some problem in connection: " . $e->getMessage();
              }
                }
public function closeConnection() {
        $this->con = null;
    }

}
?> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，注意数据库是 MySQL。但是，如果你需要将数据库改为 MS SQL Server，唯一的改变就是将 *mysql* 替换为 *mssql* 。

注意: PDO 可以处理异常。因此，总是将其操作包装在 try and catch 块中。

## 表格创建

为了创建一个表，我将首先声明一个查询字符串，然后用 exec 函数*执行*。请注意，这不会返回任何数据。

```
<?php
include_once 'connection.php';
try
{
                $database = new Connection();
                $db = $database->openConnection();

                // sql to create table
                $sql = "CREATE TABLE `Student` ( `ID` INT NOT NULL AUTO_INCREMENT , `name`VARCHAR(40) NOT NULL , `last_ame` VARCHAR(40) NOT NULL , `email` VARCHAR(40)NOT NULL , PRIMARY KEY (`ID`)) ";

                // use exec() because no results are returned
                $db->exec($sql);
                echo "Table Student created successfully";

                $database->closeConnection();
}
catch (PDOException $e)
{
                echo "There is some problem in connection: " . $e->getMessage();
}

?> 
```

Enter fullscreen mode Exit fullscreen mode

## 数据插入

要使用 PDO 向表中插入数据，我将首先使用准备好的语句来保护查询免受 SQL 注入的影响。接下来，我将使用 *execute* 函数执行这个查询。

```
<?php
include_once 'connection.php';
try
{
                $database = new Connection();
                $db = $database->openConnection();

                // inserting data into create table using prepare statement to prevent from sql injections
                $stm = $db->prepare("INSERT INTO student (ID,name,last_name,email) VALUES ( :id, :name, :lastname, :email)") ;

    // inserting a record
    $stm->execute(array(':id' => 0 , ':name' => 'Saquib' , ':lastname' => 'Rizwan' , ':email' => 'saquib.rizwan@cloudways.com'));

                echo "New record created successfully";
}

catch (PDOException $e)
{
                echo "There is some problem in connection: " . $e->getMessage();
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

## 选择数据

为了选择数据，首先创建一个查询字符串，然后在一个 *foreach* 循环中执行它，从表
中获取记录

```
<?php
include_once 'connection.php';
try
{
                $database = new Connection();
                $db = $database->openConnection();
                $sql = "SELECT * FROM student " ;
                foreach ($db->query($sql) as $row) {
            echo " ID: ".$row['ID'] . "<br>";
            echo " Name: ".$row['name'] . "<br>";
            echo " Last Name: ".$row['last_name'] . "<br>";
            echo " Email: ".$row['email'] . "<br>";
        }

}

catch (PDOException $e)
{
                echo "There is some problem in connection: " . $e->getMessage();
}

?> 
```

Enter fullscreen mode Exit fullscreen mode

## 用 PDO 更新数据

为了更新表中的一条记录，首先声明一个查询字符串，然后用 *exec* 函数执行它。

```
<?php
include_once 'connection.php';
try
{
                $database = new Connection();
                $db = $database->openConnection();
                $sql = "UPDATE `student` SET `name`= 'yourname' , `last_name` = 'your lastname' , `email` = 'your email' WHERE `id` = 8" ;
                $affectedrows  = $db->exec($sql);
    if(isset($affectedrows))
    {
                echo "Record has been successfully updated";
    }       
}
catch (PDOException $e)
{
                echo "There is some problem in connection: " . $e->getMessage();
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

## 用 PDO 删除数据

为了从表中删除一条记录，首先声明一个查询字符串，然后用 *exec* 函数执行它。

```
<?php
include_once 'connection.php';
try
{
                $database = new Connection();
                $db = $database->openConnection();
                $sql = "DELETE FROM student WHERE `id` = 8" ;
                $affectedrows  = $db->exec($sql);
    if(isset($affectedrows))
    {
                echo "Record has been successfully deleted";
    }       
}
catch (PDOException $e)
{
                echo "There is some problem in connection: " . $e->getMessage();
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

PDO 是数据访问层，它极大地简化了连接和使用数据库的过程。使用 PDO，您可以轻松地执行所有与数据库相关的操作，而无需处理数据库级问题的本质。如果你有问题或者想加入讨论，请在下面留言