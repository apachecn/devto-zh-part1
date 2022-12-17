# 战略设计模式

> 原文：<https://dev.to/sarfraznawaz2005/strategy-design-pattern-3114>

在这篇文章中，我们将探索什么是策略设计模式，它能解决什么问题，但是首先，什么是设计模式？维基百科是这样定义的:

> 在软件工程中，设计模式是在软件设计的给定上下文中，对一个**常见问题**的通用**可重用解决方案**。

因此，设计模式只不过是针对你在编程时可能面临的常见问题的一个可重用的解决方案。

## 概述

我们在编程中面临的一个常见问题就是根据*不同的条件/输入来做决策。*

假设我们已经创建了自己的框架，我们希望能够允许用户选择*不同的*类型的日志记录方法，如记录到文件、数据库或电子邮件。

## 举例

首先，我们为实现公共接口的框架创建日志类:

```
interface InterfaceLogger {
    function log($message);
}

class FileLogger implements InterfaceLogger {
    public function log($message) {
        // code to write logging information to file system
    }   
}

class DatabaseLogger implements InterfaceLogger {
    public function log($message) {
        // code to write logging information to database
    }   
}

class EmailLogger implements InterfaceLogger {
    public function log($message) {
        // code to write logging information to email
    }   
} 
```

让我们假设我们框架的用户被允许通过输入`file`、`database`或`email`通过一些配置文件选择一个特定的记录器，并且我们的框架能够知道它:

```
$loggerType = App::getConfig('logger'); 
```

现在，使用策略设计模式，我们可以很容易地找到使用哪个日志记录器:

```
switch($loggerType) {
    case "file":
        $logger = new FileLogger();
        break;
    case "database":
        $logger = new DatabaseLogger();
        break;
    case "email":
        $logger = new EmailLogger();
        break;
    default:
        $logger = new FileLogger();
} 
```

然后，在我们的整个框架中，我们可以使用`$logger->log()`方法，它会自动知道使用哪种日志记录器类型。

正如你可能已经注意到的，策略模式是非常简单的一种模式，我们可能已经写了很多次类似的代码，尽管没有意识到它实际上是某种设计模式。

[![](img/914dd08554820274f81eeab0c82d4673.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yRIWovBO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/pQDT_rs0aXU)