# 在 PHP 中抛出自己的库异常

> 原文：<https://dev.to/sarfraznawaz2005/throwing-your-own-library-exceptions-in-php-1j62>

在[之前的文章](http://codeinphp.github.io/post/exceptions-are-bad-yet-awesome/)中，我们看到了错误和异常之间的区别，以及异常是如何有用的，并创建了我们的自定义异常处理程序。在本帖中，我们将看看如何创建特定于我们的应用程序、库或公司的自定义异常。我们还讨论了作为最佳实践应该尽可能在您的代码中使用的 [SPL 异常](http://php.net/manual/en/spl.exceptions.php)。

## 背景

让我们从一点背景开始。如果你使用 PHP 的 DOM、PDO、MySQLi 或其他扩展或框架，如 Symphony、Laravel、Slim 或任何其他第三方库，你可能会注意到它们通常会抛出自己的异常:

```
$dsn = 'mysql:dbname=testdb;host=127.0.0.1';
$user = 'dbuser';
$password = 'dbpass';

$dbh = new PDO($dsn, $user, $password);
$dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION); 
```

如果数据库连接错误，会抛出`PDOException` :

```
PDOException: SQLSTATE[HY000] [1045] Access denied for user 'dbuser'@'localhost' 
```

`PDOException`从何而来？或者 PDO 是如何给出那个例外的？很简单。正如我在[之前的帖子](http://codeinphp.github.io/post/exceptions-are-bad-yet-awesome/)中指出的那样， [`Exception`](http://php.net/manual/en/class.exception.php) 是一个像任何其他普通类一样的类，可以被[扩展](http://php.net/manual/en/language.exceptions.extending.php)。这正是 PDO 在这里所做的，它简单地`extends`了`Exception`类，所以 PDO 扩展应该在引擎盖下做类似这样的事情，以便能够抛出`PDOException` :

```
class PDOException extends Exception {} 
```

而这就是*通常的* it。然后 PDO 现在应该抛出新的异常，而不是`Exception` :

```
// something went wrong
throw new PDOException('message here'); 
```

然后 PDO 的用户看到那些`PDOExceptions`。

现在我们知道`PDOException`实际上扩展了`Exception`，我们可以得出结论，我们可以通过使用`PDOException`或`Exception`通过*嵌套的* catch 块来捕捉 PDO 抛出的异常。如果`PDOException`没有捕捉到异常，将退回到`Exception` :

```
try{
    // any PDO code here
}
catch(PDOException $e){
    // handle PDOException
}
catch(Exception $e){
    // handle Exception
} 
```

## 抛出自己的异常

如上所述，我们可以通过扩展`Exception`类来抛出自己的异常:

```
class MyAwesomeLibraryException extends Exception {} 
```

或者

```
class MyCompanyException extends Exception {} 
```

这很简单，但是有人可能会问为什么抛出自定义异常？我可以看到这些原因:

*   在代码层次结构中，很容易识别哪个东西(库、类、扩展等)产生了异常
*   这有助于原始库的开发者容易地发现他们代码中的问题
*   它像 PDO、DOM 等一样标记你的库异常。

现在，作为某个库/应用程序的开发人员，每当我们发现需要抛出异常时，我们只需抛出自己定制的异常:

```
// something wrong, throw our custom exception
throw new MyAwesomeLibraryException('some message'); 
```

当然，如果您愿意，您的应用程序也可以有许多异常类型。

## 优先考虑自己的例外

假设我们想要创建自己的 ORM 库，名为 **SuperORM** ，它使用了 PDO。我们首先创建自定义异常:

```
class SuperORMException extends PDOException {} 
```

现在我们在需要的地方抛出整个 ORM 的异常。但是因为我们在幕后使用 PDO，我们也得到它的`PDOException`，我们不想直接向我们超级库的消费者展示这个*，我们希望能够首先向他们展示我们自己的异常类型。我们可以这样做:* 

```
class SuperORMException extends PDOException {}

class SuperORM {
    public function connect($dsn, $user, $password) {
        // try connecting to database
        try {
            $dbh = new PDO($dsn, $user, $password);
            $dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        }
        catch (PDOException $e) {
            throw new SuperORMException($e->getMessage(), null, $e);
        }        
    }
}

$dsn = 'mysql:dbname=testdb;host=127.0.0.1';
$user = 'dbuser';
$password = 'dbpass';

$superORM = new SuperORM;
$superORM->connect($dsn, $user, $password); 
```

这将导致我们的`SuperORMException`:

`SuperORMException: SQLSTATE[HY000] [1045] Access denied for user 'dbuser'@'localhost'`

我们超级用户现在可以捕捉到我们的异常:

```
try {
    $dsn = 'mysql:dbname=testdb;host=127.0.0.1';
    $user = 'dbuser';
    $password = 'dbpass';

    $superORM = new SuperORM;
    $superORM->connect($dsn, $user, $password);
}
catch (SuperORMException $e) {
    echo $e->getMessage();
} 
```

这确保了库的消费者现在知道 SuperORM 将总是抛出类型为`SuperORMException`的异常。

我们看到,`SuperORMException`扩展了`PDOException`,而`PDOException`扩展了`Exception`,这给了消费者捕捉这些类型异常的机会，比如:

```
try {
    $dsn = 'mysql:dbname=testdb;host=127.0.0.1';
    $user = 'dbuser';
    $password = 'dbpass';

    $superORM = new SuperORM;
    $superORM->connect($dsn, $user, $password);
}
catch (SuperORMException $e) {
    // code to catch exception
}
catch (PDOException $e) {
    // code to catch exception
}
catch (Exception $e) {
    // code to catch exception
} 
```

这就是你抛出自定义异常的方法。事实上，各种框架中的一些 ORM 或数据库就是这样做的，它们抛出自己的异常供消费者捕捉。

## 不要总是抛出你的自定义异常

我的意思是，标准 PHP (SPL)中有某些异常类型，称为 [SPL 异常](http://php.net/manual/en/spl.exceptions.php)。它们是为了*特定的*原因而被抛出的，被认为是最佳实践。你应该在适当的时候抛出 SPL 异常*而不是你自己定制的异常。他们在这里:*

*   [逻辑异常](http://php.net/manual/en/class.logicexception.php)(扩展[异常](http://php.net/manual/en/class.exception.php))
    *   [badfunctioncalleexception](http://php.net/manual/en/class.badfunctioncallexception.php)
    *   [BadMethodCallException](http://php.net/manual/en/class.badmethodcallexception.php)
    *   [域名异常](http://php.net/manual/en/class.domainexception.php)
    *   [无效参数异常](http://php.net/manual/en/class.invalidargumentexception.php)
    *   [长度异常](http://php.net/manual/en/class.lengthexception.php)
    *   [OutOfRangeException](http://php.net/manual/en/class.outofrangeexception.php)
*   [运行时间异常](http://php.net/manual/en/class.runtimeexception.php)(扩展[异常](http://php.net/manual/en/class.exception.php))
    *   [out of bounds 异常](http://php.net/manual/en/class.outofboundsexception.php)
    *   [溢出异常](http://php.net/manual/en/class.overflowexception.php)
    *   [范围异常](http://php.net/manual/en/class.rangeexception.php)
    *   [下溢异常](http://php.net/manual/en/class.underflowexception.php)
    *   [意外值异常](http://php.net/manual/en/class.unexpectedvalueexception.php)

可以看出，这些异常可以大致分为两大类:`LogicException`和`RuntimeException`。所有这些异常从它们的名字来看都是不言自明的。例如，`LogicException`代表由代码中的逻辑错误导致的任何错误，`RuntimeException`代表脚本运行后导致的任何错误，例如运行时错误；类似地，如果你的类中的一个方法不存在，那么应该抛出`BadMethodCallException`异常，等等。

这些 SPL 异常是对 PHP 核心的极好补充，因为以前如果调用了一些不存在的方法，你通常会通过类似于
的消息通知用户

```
throw new Exception('Method does not exist!'); 
```

所以你通过这些信息交流不同类型的问题。这里的问题是我们需要特定的例外；如果方法不存在，我们需要`BadMethodCallException`异常。这清楚地告诉开发人员这是什么类型的异常，以及*为什么*它可能会发生。另一个好处是，这种异常甚至对不说英语的开发人员也很有用，他们也很容易发现问题。因此，您必须针对不同的情况使用 SPL 例外。

[![](img/d721621d9c99c5490d471587057358f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ybloqbYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/_7BKEaNBRKY)*