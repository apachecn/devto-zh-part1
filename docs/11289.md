# 工厂设计模式

> 原文：<https://dev.to/sarfraznawaz2005/factory-design-pattern-2697>

## 概述

工厂设计模式就像现实世界中的工厂一样工作，它创建一些东西供他人使用。在 OOP 的上下文中，它有助于创建和实例化对象。

## 举例

假设我们的应用程序基于需求使用不同类型的数据库，比如 MySQL、PostgreSQL 和 SQLite。我们的类看起来像这样:

MySQL:

```
class MySQLDB
{
    public function setHost($host)
    {
        // code
    }
    public function setDB($db)
    {
        // code
    }
    public function setUserName($user)
    {
        // code
    }
    public function setPassword($pwd)
    {
        // code
    }
    public function connect()
    {
        // code
    }
} 
```

PostgreSQL:

```
class PostgreSQLDB
{
    public function setHost($host)
    {
        // code
    }
    public function setDB($db)
    {
        // code
    }
    public function setUserName($user)
    {
        // code
    }
    public function setPassword($pwd)
    {
        // code
    }
    public function connect()
    {
        // code
    }
} 
```

...对于 SQLite 数据库也是如此。

然后我们根据配置实例化不同的对象:

```
if (Config::item('db_type') === 'mysql') {
    $DB = new MySQLDB();
    $DB->setHost("host");
    $DB->setDB("db");
    $DB->setUserName("user");
    $DB->setPassword("pwd");
    $DB->connect();
}
elseif (Config::item('db_type') === 'postgre') {
    $DB = new PostgreSQLDB();
    $DB->setHost("host");
    $DB->setDB("db");
    $DB->setUserName("user");
    $DB->setPassword("pwd");
    $DB->connect();
}
elseif (Config::item('db_type') === 'sqlite') {
    $DB = new SQLiteDB();
    $DB->setHost("host");
    $DB->setDB("db");
    $DB->setUserName("user");
    $DB->setPassword("pwd");
    $DB->connect();
} 
```

一般来说，上述方法是可行的，但在相当大的应用中会产生问题。上面的代码有这些问题:

*   如果您添加更多的数据库类型，代码会不断增长，使其变得复杂
*   代码是用数据库类型名硬编码的
*   整个过程变得乏味

为了解决这些问题，我们可以创建一个中央工厂对象来为我们创建这些对象:

```
class DBFactory
{
    protected $driver = null;

    public function setDriver($driver)
    {
        $this->driver = $driver;
    }

    public function makeDB($host, $user, $pass, $dbname)
    {
        if ($this->driver === 'mysql') {
            $DB = new MySQLDB();
        }
        elseif ($this->driver === 'postgre') {
            $DB = new PostgreSQLDB();
        }
        elseif ($this->driver === 'sqlite') {
            $DB = new SQLiteDB();
        }

        $DB->setHost($host);
        $DB->setDB($dbname);
        $DB->setUserName($user);
        $DB->setPassword($pass);
        $DB->connect();

        return $DB;
    }
} 
```

然后使用它:

```
$dbFactory = new DBFactory;
$dbFactory->setDriver(Config::item('db_type'));
$DB = $dbFactory->makeDB("host", "db", "user", "pwd"); 
```

仅此而已。你不需要修改上面的代码，它会一直保持不变，我们已经把复杂的对象创建逻辑从工厂中分离出来，这使得我们的代码更容易使用。

请注意，工厂设计模式有一些变化，如`simple factory`、`abstract factory`和`factory method`。在这个例子中，我们使用了`factory method` eg，我们使用了一个名为`makeDB()`的*方法*来创建我们的对象。其他变化几乎是相似的；例如使用`abstract factory`，你实际上创建了一个抽象类，然后所有的具体类都应该扩展它。它只是加强了相似具体类之间的通用性。

> **注意:**在现代，借助于[依赖注入容器和服务定位器](http://ralphschindler.com/2012/10/10/di-dic-service-locator-redux)，对象创建变得非常简单。PHP 有相当多的[芯片可供选择。](http://www.sitepoint.com/php-dependency-injection-container-performance-benchmarks/)

[![](img/61be9bf86a499d7132bceff76ba99361.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E3R1Uxb8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/a8onGdoULcg)