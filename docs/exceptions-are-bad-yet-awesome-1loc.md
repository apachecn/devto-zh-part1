# 例外是不好的，但令人敬畏！

> 原文：<https://dev.to/sarfraznawaz2005/exceptions-are-bad-yet-awesome-1loc>

## 概述

乍一想，**错误**和**异常**这两个词给人的总体感觉是一样的，或者统称为*错误*，尤其是对初学者而言。例如，当我们看到这个:

`Fatal error: Call to undefined method Foo::bar()`

或者这个:

`Exception: Method is not callable by this object`

大多数初学开发的人会认为这些只是需要修复的错误，从表面上看可能是对的，因为这两个消息都是不好的，无论如何都需要修复，但实际上它们是不同的东西。第一个是**错误**，而后一个是**异常**。一旦我们理解了错误和异常都有*以及如何成功地处理它们，我们肯定能写出更好的代码。*

在本帖中，我们将看到如何处理这两个问题，甚至创建我们自己的自定义错误和异常处理程序，以便在遵循最佳实践的同时，更好地控制它们的显示或处理方式。

# 错误和异常的区别

**错误**

错误是由编程语言发出的错误，您需要修复它们。

可能有语法错误或逻辑错误。在 PHP 中，有`ERROR`、`PARSE`、`WARNING`、`NOTICE`、`STRICT`等不同*级的错误。一些错误级别会暂停 PHP 脚本的进一步执行(比如`FATAL`错误)，而其他错误级别则允许脚本继续执行，同时显示有用的信息，这些信息可能也需要修复或注意，比如`WARNING`或`NOTICE`。最后，还有其他错误级别，可以判断某个特定函数是否被弃用(`DEPRECATED`)或者是否遵循了标准(`STRICT`)。*

错误可以被转换成用户抛出的异常，但仍有一些是可恢复的，而另一些不是，因为它们是由核心编程语言发出的

我们可以通过 [`trigger_error()`](http://php.net/manual/en/function.trigger-error.php) 功能发出自定义/用户错误

我们可以使用 [`set_error_handler()`](http://php.net/manual/en/function.set-error-handler.php) 为所有错误创建自定义错误处理程序

*   [`error_get_last`](http://php.net/manual/en/function.error-get-last.php) 函数可以用来获取 PHP 代码中最近发生的任何错误。 [`$php_errormsg`](http://php.net/manual/en/reserved.variables.phperrormsg.php) 变量可用于获取之前的错误信息。

**异常情况**:

异常是面向对象的错误处理方法，由代码/开发人员有意抛出，应该使用`try - catch -finally`块来处理/捕捉

一个`Exception`是一个标准类，可以像其他类一样使用，也可以扩展。

异常可以有许多类型(通过子类)，而错误只能有上面提到的级别。

异常可以在调用堆栈中的任何点被捕获，也可以在根/默认异常处理程序中被捕获。相比之下，错误只在预定义的错误处理程序中处理。

我们可以通过使用`throw new Exception(...)`抛出自定义/用户异常

*   我们可以使用 [`set_exception_handler()`](http://php.net/manual/en/function.set-exception-handler.php) 为所有异常创建定制的异常处理程序

## 一般惯例

如今，通常(也是更好的)做法是总是从代码中抛出异常(即使是错误),以便可以在调用者脚本中捕捉和处理它们。当然，如果我们抛出一个错误异常`FATAL`，我们不能从中恢复，但我们仍然可以提供面向对象的方法调用脚本。如果您使用过`MySQL`扩展，您会注意到如果出现问题，它会发出正常的错误；以下是一个无法连接到数据库的示例:

`Warning: mysql_connect(): Access denied for user 'root'@'localhost'`

请注意，它发出的错误级别为`Warning`。这只是函数`mysql_connect`的一个例子，但是`MySQL`扩展的其他函数也以同样的方式发出错误，并且可以用 [`mysql_error()`](http://php.net/manual/en/function.mysql-error.php) 函数捕获。

但是如果你使用 MySQL 的改进版本，叫做`MySQLi`或者甚至`PDO`，你会注意到他们现在也可以抛出异常，这里是同样的例子，如果使用`mysqli`不能连接到数据库:

```
mysqli_report(MYSQLI_REPORT_STRICT); // tell mysqli to generate exceptions as well
mysqli_connect('localhost', 'root', 'wrongPassword', 'test'); 
```

它会给你错误和异常:

`Fatal error: Uncaught exception 'mysqli_sql_exception' with message 'Access denied for user 'root'@'localhost'`

`mysqli_sql_exception: Access denied for user 'root'@'localhost' (using password: YES)`

所以这些天来，你使用的任何好的编写的包或库或一些扩展，它很可能会生成自己类型的异常，这样它们就可以被捕获并被优雅地处理。

## 异常有什么帮助？

我们通过例子来了解一下。假设您想使用`mysql`连接到数据库，您通常会这样做:

```
mysql_connect('localhost', 'root', 'wrongPassword', 'test'); 
```

如果无法连接到数据库，您将收到错误消息:

`Warning: mysql_connect(): Access denied for user 'root'@'localhost'`

现在，你能做的只有*的事情就是转到你的代码并编辑它以指定正确的数据库凭证。*

 *现在让我们用`mysqli` :
做同样的事情

```
mysqli_report(MYSQLI_REPORT_STRICT); // tell mysqli to generate exceptions as well
mysqli_connect('localhost', 'root', 'wrongPassword', 'test'); 
```

这将生成一个错误和异常，如前所示。由于产生了异常，我们可以捕捉它并显示消息:

```
try {    
    mysqli_connect('localhost', 'root', 'wrongPassword', 'test');
} catch (mysqli_sql_exception $e) {
    echo $e->getMessage();
} 
```

异常在这里有用的原因是因为您有机会优雅地捕捉异常。在`catch`块中，我们可以任意处理异常。举例来说，假设我们想用正确的密码再次连接到数据库:

```
mysqli_report(MYSQLI_REPORT_STRICT);

try {    
    mysqli_connect('localhost', 'root', 'wrongPassword', 'test');
} catch (mysqli_sql_exception $e) {
    mysqli_connect('localhost', 'root', 'rightPassword', 'test');
} 
```

感谢 exception，我们能够捕获它并以我们需要的方式处理它，我们现在连接到数据库，这在以前使用`mysql`的例子中是不可能的，它只发出一个错误，我们不能做太多。当然，在现实世界的应用程序中，你可能没有不同的密码来连接数据库，但是这个例子只是给出了一个*如何*异常有用的想法。

作为另一个例子，假设我们想要使用 [SimpleXML](http://php.net/manual/en/book.simplexml.php) (也可以抛出异常)读取某个网站的 feed/rss，并在一个数组中存储 10 篇文章:

```
$feedUrl = 'http://some_feed_url';
$feeds = file_get_contents($feedUrl);
$xml = new SimpleXmlElement($feeds);

$articles = array();
foreach ($xml->channel->item as $item) {
   $item = (array) $item;
   $articles[] = array('title' => $item['title'], 'link' => $item['link']);
}

$data['articles'] = array_slice($articles, 0, 10); 
```

只要提要 url 是正确的并且有帖子，这就可以工作，但是如果 url 是错误的，您将会看到脚本生成的`Fatal`错误和`Exception`:

`Fatal error: Uncaught exception 'Exception' with message 'String could not be parsed as XML`

`Exception: String could not be parsed as XML`

由于是`FATAL`错误，我们的脚本在那一点死亡，我们不能做任何事情。即使提供的提要 url 是错误的，我们如何确保我们的脚本继续工作并运行该提要代码下的任何代码？当然，我们需要`catch`这个异常，因为正如我们看到的，它也生成了一个`Exception` :

```
try {
    $feedUrl = 'http://some_feed_url';
    $feeds = file_get_contents($feedUrl);
    $xml = new SimpleXmlElement($feeds);

    $articles = array();
    foreach ($xml->channel->item as $item) {
       $item = (array) $item;
       $articles[] = array('title' => $item['title'], 'link' => $item['link']);
    }

    $data['articles'] = array_slice($articles, 0, 10);
} catch (Exception $e) {

}

echo 'Hello World'; 
```

现在，既然我们已经将代码包装在`try-catch`中并捕获了异常，那么下面的代码应该还会运行。在这种情况下，即使提要 url 是错误的，您仍然应该看到`Hello World`消息。我们的剧本没有死，继续执行。所以这两个例子应该让我们知道异常在使用时有多有用。

## 如何将错误转化为异常？

为此，我们可以使用 [`set_error_handler()`](http://php.net/manual/en/function.set-error-handler.php) 函数并抛出 [`ErrorException`](http://php.net/manual/en/class.errorexception.php) 类型的异常，类似于:

```
set_error_handler(function ($errorNumber, $errorText, $errorFile, $errorLine ) 
{
    throw new ErrorException($errorText, 0, $errorNumber, $errorFile, $errorLine);
}); 
```

现在有了自定义异常，您可以做如下事情:

```
try {
    // wrong url
    file_get_contents('http://wrong_url');
} catch (ErrorException $e) {
    // fix the url
    file_get_contents('http://right_url');
} 
```

所以在`catch`块中，我们现在能够修复`file_get_contents`函数的 URL。如果没有这个异常，我们只能通过使用[错误控制操作符@](http://php.net/manual/en/language.operators.errorcontrol.php) 来抑制错误。

## 通用异常处理程序

既然我们已经看到了异常是多么有用，以及如何将错误转换为异常，我们可以创建我们的自定义通用异常处理程序，它可以在整个应用程序中使用。它总是会产生异常而不是错误。它会做以下事情:

1 -允许我们告诉它我们的环境，可以是`development`或`production`

2 -在`production`环境中，它会将所有错误记录到一个文件中，而不是显示在屏幕上

3 -在`development`环境下，将在屏幕
上显示所有错误

```
/**
 * A class that handles both errors and exceptions and generates an Exception for both.
 *
 * In case of "production" environment, errors are logged to file
 * In case of "development" environment, errors are echoed out on screen
 *
 * Usage:
 *
 * new ExceptionHandler('development', '/myDir/logs');
 *
 * Note: Make sure to use it on very beginning of your project or bootstrap file.
 *
 */
class ExceptionHandler {
    // file path where all exceptions will be written to
    protected $log_file = '';
    // environment type
    protected $environment = '';

    public function __construct($environment = 'production', $log_file = 'logs')
    {
        $this->environment = $environment;
        $this->log_file = $log_file;

        // NOTE: it is better to set ini_set settings via php.ini file instead to deal with parse errors.
        if ($this->environment === 'production') {
            // disable error reporting
            error_reporting(0);
            ini_set('display_errors', false);
            // enable logging to file
            ini_set("log_errors", true);
            ini_set("error_log", $this->log_file);
        }
        else {
            // enable error reporting
            error_reporting(E_ALL);
            ini_set('display_errors', 1);
            // disable logging to file
            ini_set("log_errors", false);
        }

        // setup error and exception handlers
        set_error_handler(array($this, 'errorHandler'));
        set_exception_handler(array($this, 'exceptionHandler'));        
    }

    public function exceptionHandler($exception)
    {
        if ($this->environment === 'production') {
            error_log($exception, 3, $this->log_file);
        }

        throw new Exception('', null, $exception);
    }

    public function errorHandler($error_level, $error_message, $error_file, $error_line)
    {
        if ($this->environment === 'production') {      
            error_log($message, 3, $this->log_file);
        }

        // throw exception for all error types but NOTICE and STRICT
        if ($error_level !== E_NOTICE && $error_level !== E_STRICT) {
            throw new ErrorException($error_message, 0, $error_level, $error_file, $error_line);
        }        
    }
} 
```

测试一下:

```
// register our error and exceptoin handlers
new ExceptionHandler('development', 'logs');

// create error and exception for testing
trigger_error('Iam an error but will be converted into ErrorException!');
throw new Exception('I am an Exception anyway!'); 
```

这是*基本*通用错误和异常处理程序类的一个例子。实际上，您可以做更多的事情，比如定制消息的显示，获取堆栈跟踪以及导致它的代码，为那些事件发生的时间创建时间戳，等等。为了获得这些好处，你应该查阅官方文档。在你的代码中的任何一点，你想恢复原来 PHP 的错误和异常处理程序，你可以使用 [`restore_error_handler()`](http://php.net/manual/en/function.restore-error-handler.php) 和 [`restore_exception_handler()`](http://php.net/manual/en/function.restore-exception-handler.php) 函数。

## 结论

例外不例外！

* * *

顺便说一句，我个人希望有一天能为处理错误和异常及其最佳实践创建一个 PSR 标准，因为它们是任何 PHP 代码不可或缺的一部分。

[![](img/f4a60ae7a0aaf6f8ac48445231800a96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MyluDjrX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/f5VSKevMhF4)*