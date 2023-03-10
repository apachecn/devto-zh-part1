# 重构，一个具有类抽象的解决方案

> 原文：<https://dev.to/sebastianr1982/refactor-a-solution-with-class-abstraction>

在开发我的小 PHP 框架时，我遇到了代码重复的情况。情况显然无法解决，因为代码涉及框架的两个不同部分。

### 乱七八糟

我有两个类，一个用于连接到持久存储，另一个用于连接到缓存。类返回不同的对象，但是以相同的方式执行操作。

代码没有优化，但这是另一个故事。我希望专注于复制和重构。

存储工厂:

```
<?php

/**
 * Storage Factory.
 */
class StorageFactory
{
    private $driver;

    private $supportedDriver = [
        'pdo'     => PdoStorage::class,
        'mysqli'  => MysqliStorage::class,
        'mongodb' => MongoDbStorage::class,
    ];

    private $options;

    public function __construct(string $driver, array $options)
    {
        $this->driver = $driver;
        $this->options = $options;
    }

    public function getConnection() : StorageInterface
    {
        $driver = $this->driver;
        $options = $this->options;

        if (isset($this->supportedDriver[$driver])) {
            $storageClass = $this->supportedDriver[$driver];

            return new $storageClass($options);
        }

        throw new InvalidArgumentException("[$driver] not supported.");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和缓存工厂:

```
<?php

/**
 * Cache Factory.
 */
class CacheFactory
{
    private $driver;

    private $supportedDriver = [
        'disk'       => DiskCache::class,
        'memcached'  => MemcachedCache::class,
    ];

    private $options;

    public function __construct(string $driver, array $options)
    {
        $this->driver = $driver;
        $this->options = $options;
    }

    public function get() : CacheInterface
    {
        $driver = $this->driver;
        $options = $this->options;

        if (isset($this->supportedDriver[$driver])) {
            $cache = $this->supportedDriver[$driver];

            return new $cache($options);
        }

        throw new InvalidArgumentException("[$driver] not supported.");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

先前的代码是一个明显的重复例子:(

### 提问开始

如何解决这个问题？

缓存是一种存储类型，是一种非常快速的存储。我可以将所有的类合并到一个工厂类中，但是我的框架中的`*Storage`类和`*Cache`类实现了不同的接口。

此外，我希望使用 PHP 返回类型声明我不能有一个方法返回两种不同类型的对象。

我分析代码...

*班级的差异在哪里？*

我必须隔离代码中的差异:

```
<?php

// Storage class
private $supportedDriver = [
    'pdo'     => PdoStorage::class,
    'mysqli'  => MysqliStorage::class,
    'mongodb' => MongoDbStorage::class,
];

// Cache class
private $supportedDriver = [
    'disk'       => DiskCache::class,
    'memcached'  => MemcachedCache::class,
];

// Storage class
public function getConnection() : StorageInterface
{
    //code
}

// Cache class
public function get() : CacheInterface
{
    //code
} 
```

Enter fullscreen mode Exit fullscreen mode

*现在呢？*

现在我知道了不同之处，必须将它们分成两个具体的实现，并将重复的代码移到一个抽象类中。

抽象类:

```
<?php

/**
 * Abstract Storage Factory.
 */
abstract class AbstractStorageFactory
{
    protected $driver;

    protected $supportedDriver = [];

    protected $options;

    public function __construct(string $driver, array $options)
    {
        $this->driver = $driver;
        $this->options = $options;
    }

    protected function returnStorageObject()
    {
        $driver = $this->driver;
        $options = $this->options;

        if (isset($this->supportedDriver[$driver])) {
            $class = $this->supportedDriver[$driver];

            return new $class($options);
        }

        throw new InvalidArgumentException("[$driver] not supported.");
    }

    /**
     * Get storage object.
     *
     * @return object
     */
    abstract public function get();
} 
```

Enter fullscreen mode Exit fullscreen mode

混凝土工厂:

```
<?php

/**
 * Storage Factory.
 */
class StorageFactory extends AbstractStorageFactory
{
    protected $supportedDriver = [
        'pdo'     => PdoStorage::class,
        'mysqli'  => MysqliStorage::class,
        'mongodb' => MongoDbStorage::class,
    ];

    public function get() : StorageInterface
    {
        return $this->returnStorageObject();
    }
}

/**
 * Cache Factory.
 */
class CacheFactory extends AbstractStorageFactory
{
    protected $supportedDriver = [
        'disk'       => DiskCache::class,
        'memcached'  => MemcachedCache::class,
    ];

    public function get() : CacheInterface
    {
        return $this->returnStorageObject();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 终于

代码重复消失了，我使用了 PHP 返回类型。

我写这篇小文章不是为了代码，而是为了分享得出最终结果的推理过程。