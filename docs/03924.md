# 静态和类属性

> 原文：<https://dev.to/cr0wst/statics-and-class-properties-4pgb>

在上一篇文章中，我谈到了从容器中检索的单体服务，以及它们如何影响队列工作器。在文章的最后，我添加了一个关于静态的小评论，以及它们是如何引起麻烦的。我想更详细地介绍一下类属性。

## 两种类型的类属性

也许阶级属性在这里不是正确的措辞，但这就是我要提到的。在 PHP 中，我们有绑定到对象的**实例**的*实例*属性，我们有绑定到**类**的*静态*属性。

### 实例属性

这些是需要实例引用的属性。为每个实例以及该实例上的每个属性分配内存。

```
class Example {
    public $propertyOne = 'one';
}

$example = new Example;
echo $example->propertyOne; // 'one' 
```

### 静态属性

这些是绑定到类的属性。它们不需要类的实例。只有一个**单个**存储单元被分配给参考。你可以把这些看作全局变量，因为你可以从任何地方引用它们。

```
class Example {
    public static $propertyOne = 'one';
}

echo Example::$propertyOne; // 'one' 
```

## `zval`

PHP 使用`zval`在内存中存储对象和引用。如果你安装了 [xdebug](https://xdebug.org/) 工具，你可以使用`xdebug_debug_zval`方法来收集一些关于特定对象的信息。

下面是示例类的新版本:

```
class Example {
    public $propertyOne;
    public static $propertyTwo;

    public function __construct($propertyOne)
    {
        $this->propertyOne = $propertyOne;
    }
} 
```

如果我们按以下方式使用它:

```
$example1 = new Example('foo');
$example2 = new Example('bar');

print xdebug_debug_zval('example1');
print xdebug_debug_zval('example2'); 
```

打印出以下结果:

```
example1: (refcount=1, is_ref=0)=class Example { 
    public $propertyOne = (refcount=0, is_ref=0)='foo' 
}

example2: (refcount=1, is_ref=0)=class Example { 
    public $propertyOne = (refcount=0, is_ref=0)='bar' 
} 
```

**refcount** 是指变量被引用的次数。注意`$example1`和`$example2`都只被引用一次。

然而，如果我稍微修改一下代码:

```
$example1 = new Example('foo');
$example2 = new Example('bar');
$example3 = $example1; 
```

您可以看到 refcount 在`$example1`增加。

```
example1: (refcount=2, is_ref=0)=class Example { 
    public $propertyOne = (refcount=0, is_ref=0)='foo' 
}

example2: (refcount=1, is_ref=0)=class Example { 
    public $propertyOne = (refcount=0, is_ref=0)='bar' 
} 
```

PHP 实际上使用这个值来决定在垃圾收集期间应该清理哪些引用

另外需要注意的是，`static`属性`$propertyTwo`没有显示在`xdebug_debug_zval`的输出中。这是因为它不依赖于实例，不应该从实例访问。

## PHP 可变生命周期

您可以阅读更多关于[垃圾收集](http://php.net/manual/en/features.gc.php)过程的内容，了解在垃圾收集过程中如何清理实例变量。需要注意的是，*静态不是垃圾收集*。脚本执行完毕后，静态变量将被清除。这就是为什么，从[队列和有状态服务](https://dev.to/nexmo/queues-and-stateful-services-1gi8-temp-slug-1378948)的文章来看，静态会对队列工作者产生影响。脚本永远不会停止执行。

然而，在正常使用中，每个请求都有自己独立的静态变量。这与 Java 的*不同，在 Java 中，静态数据会一直存在，直到应用程序或服务器重启。*

## 静态用途

如果在请求期间需要将信息传递给多个类，那么静态生命周期尤其有用。通过使用*单例模式*或者通过*延迟加载和锁定*静态，可以实现最佳的内存使用。

### 延迟加载和锁定

我不知道这个的官方术语是什么。这是延迟加载和锁定属性值的结合。

```
class MemberList {
    private static $names = [];

    public static function getNames()
    {       
        return (empty(self::$names))
            ? self::$names = app(NameRetriever::class)->getNames();
            : self::$names;
    }
} 
```

在这个例子中，我从应用程序容器中注入的`NameRetriever`中检索一个名字列表。但是，我只会在第一次被请求时这样做。

对`MemberList::getNames()`方法的任何后续请求都将返回`$names`中的缓存值。如果检索姓名列表的操作过于复杂，这可能特别有用。

Laravel 实际上使用了类似的技术为单身人士服务。它们存储在一个数组中，每当从应用程序容器请求一个对象时，在实例化之前检查它们是否存在。

### 单一模式

单例是只实例化一次的类。对该类的任何后续请求都将返回同一个实例。Laravel 通过`app->singleton()`提供该功能。然而，即使您没有使用框架，也可以应用这种模式。

```
class ExampleSingleton {
    private static $instance = null;

    public static function create()
    {
        return (is_null(self::$instance))
        ? self::$instance = new self()
        : self::$instance;
    }

    private function __construct() {
        // constructor intentionally left private
    }
}
$singletonOne = ExampleSingleton::create();
$singletonTwo = ExampleSingleton::create();

if ($singletonOne === $singletonTwo) {
    echo "They are equal!";
} else {
    echo "They are not!";
} 
```

运行这个脚本返回的结果是*等于*。

这与*延迟加载和锁定*方法非常相似。只是这次我们锁定了`$instance`,以便它存储该类的一个实例，然后每次都返回它。我们也在制作`__construct`方法`private`来防止绕过单例模式。