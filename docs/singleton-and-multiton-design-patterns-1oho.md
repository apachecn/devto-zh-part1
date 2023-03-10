# 单件和多件设计模式

> 原文：<https://dev.to/sarfraznawaz2005/singleton-and-multiton-design-patterns-1oho>

## 单胎

最简单的设计模式之一是单例模式。Singleton 设计模式的目的是始终拥有某个类的一个**单个**实例。单例通常用于不一定需要多个实例的事情，比如数据库、日志或类似的事情。你总是使用单一对象的同一个实例。

这是你如何创建一个单例的方法:

```
class MySingleton
{
    public static function getInstance()
    {
        static $instance;

        if (null === $instance) {
            $instance = new self();
        }

        return $instance;
    }

    // prevent creating multiple instances due to "private" constructor
    private function __construct(){}

    // prevent the instance from being cloned
    private function __clone(){}

    // prevent from being unserialized
    private function __wakeup(){}
} 
```

用法:

```
$instance = MySingleton::getInstance(); 
```

遵循同样的模式，您可以创建自己的单件，并添加任何必要的功能。这里的**主要的**技巧是我们做了构造函数`private`，它不允许创建单例类的多个实例。

需要指出的是，单例设计模式被认为是反模式(在 PHP 世界中更是如此)，原因如下:

*   单态产生不好的全局状态；它们产生了紧密耦合
*   单例本质上是面向类的，而不是面向对象的
*   我们不知道它包含什么依赖，它隐藏了逻辑
*   因为您不能创建多个实例，所以您不能轻松地测试它们
*   PHP 运行在[无共享架构](http://en.wikipedia.org/wiki/Shared_nothing_architecture)上，这实质上意味着不像其他语言，比如 Java/。Net 中，一个 PHP 应用程序的生命就是一个 http 请求，这就是为什么在 PHP 中，单例程序没有什么 T2 意义

话虽如此，许多人认为你至少可以将单例用于单一性质的事情，如数据库或日志记录的实例(尽管这是你如何设计数据库或日志记录的个人偏好问题)，而其他人则完全避免使用单例(尤其是 *[TDD 崇拜者](http://en.wikipedia.org/wiki/Test-driven_development)* )。

这就是单身者的全部。

## 多音

顾名思义，multiton 是一种帮助您创建自身多个实例的设计模式。singleton 和 multiton 是一样的，唯一的区别是 multiton 可以存储和检索自身的多个实例。显然，多频音和单频音有同样的问题。

这里有一个多音的例子:

```
class Logger
{
    private static $instances = array();

    public static function getInstance($key)
    {
        if(!array_key_exists($key, self::$instances)) {
            self::$instances[$key] = new self();
        }

        return self::$instances[$key];
    }

    // prevent creating multiple instances due to "private" constructor
    private function __construct(){}

    // prevent the instance from being cloned
    private function __clone(){}

    // prevent from being unserialized
    private function __wakeup(){}
} 
```

用法:

```
$firstInstance = Logger::getInstance('file');
$secondInstance = Logger::getInstance('email'); 
```

[![](img/208e601196b7bd2d541ee423b8a1cfd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9q8rQ9Yn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/T6Usz7F3qJ0)