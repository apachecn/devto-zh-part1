# 使用 Composer 自动加载 php 文件背后的魔力

> 原文：<https://dev.to/blocksbylukas/the-magic-behind-autoloading-php-files-using-composer-1e19>

#### 前言

我已经好几年没有从零开始做项目了。我是说完全从零开始。默认情况下，我所有的项目都是全新的 Symfony 安装。两周前，我在录制这门课的一个讲座，当我准备一个超级简单的例子时，我遇到了一个`Class not found`错误。这让我想起了过去使用 PHP `require_once`方法的美好时光，所以我决定写一篇短文，介绍 Composer 是如何让我们的生活变得更简单，以及它实际上是如何在幕后自动加载文件的。

感谢尼尔斯和 T2【乔迪】出色的依赖性管理器和生日快乐乔迪！

#### 基线

不要想太多什么是**命名空间**。

**名称空间**基本上只是一个**类**前缀(就像操作系统中的目录)来保证类路径的唯一性。

同样，为了说明问题， **use** 语句没有做任何事情
只是别名化了你的**名称空间**，这样你就可以使用快捷方式或者在同一个文件中包含名称相同但名称空间**不同的**类**。**

例如:

```
<?php

// You can do this at the top of your Class
use Symfony\Component\Debug\Debug;

if ($_SERVER['APP_DEBUG']) {
    // So you can utilize the Debug class it in an elegant way
    Debug::enable();
    // Instead of this ugly one
    // \Symfony\Component\Debug\Debug::enable();
} 
```

#### 问题

我想从不同的目录调用封装在特定名称空间中的类的公共方法。

```
<?php

// /ComposerAutoloading/src/Christmas/Santa.php

namespace Christmas;

class Santa
{
    /**
     * @return void
     */
    public function sayIt(): void
    {
        echo "Merry Christmas && Write HQ code!";
    }
} 
```

```
<?php

// /ComposerAutoloading/src/index.php

$santa = new \Christmas\Santa();
$santa->sayIt(); 
```

```
Fatal error:  Uncaught Error: Class 'Santa' not found 
```

#### 旧时尚解

[![Old fashion James Bond movie](img/305e310886b46894b3eb28c3bd269ea1.png)](http://www.youtube.com/watch?v=ceTR67EjeyU) 
(点击播放电影场景)

```
php index.php

Fatal error:  Uncaught Error: Class 'Santa' not found 
```

因此，为了能够初始化 Santa 类，您必须通过运行`require_once($filePath)`将它导入到您的**index.php**的**全局名称空间**，它将在后台执行 [include](http://php.net/manual/en/function<br>%0A.include.php) 语句。

```
<?php

// /ComposerAutoloading/src/index.php

require_once __DIR__.'/Christmas/Santa.php';

$santa = new \Christmas\Santa();
$santa->sayIt(); // Merry Christmas && Write HQ code! 
```

这当然是一个很好的解决方案，但随着项目规模的增长，这是一个不可扩展的解决方案。

#### 作曲方案

作曲，PHP 作曲；)

每次你想包含不同文件的代码时，不用手动输入`require_once`，只需导入一个自动生成的、自我解释的 Composer 文件，名为:**autoload.php**。

就像 Symfony 在其前端控制器中所做的一样:[index.php/app.php/app_dev.php/console.php](https://github.com/symfony/demo/blob/master/public/index.php#L17)

```
<?php

// /ComposerAutoloading/src/index.php

// require_once 'Christmas/Santa.php';
require __DIR__.'/../vendor/autoload.php';

$santa = new \Christmas\Santa();
$santa->sayIt(); // Merry Christmas && Write HQ code! 
```

埃姆·卢卡斯但是...我没有任何**autoload.php**的文件也没有**厂商**的文件夹。

那是你的问题。

啊，今天是圣诞节。让我向您展示如何生成它们。

一切从 **composer.json** 文件开始。运行:

```
/ComposerAutoloading/ 
```

```
composer init 
```

和 wuala， **composer.json** 没有任何依赖性，因为我们不需要它们来自动加载工作:

```
{
    "name": "enterprise-php/composer-autoloading",
    "description": "An example how Composer works behind the scenes.",
    "type": "project",
    "authors": [
        {
            "name": "Lukas Lukac",
            "email": "services@trki.sk"
        }
    ],
    "require": {}
} 
```

现在，您可以通过运行以下命令来生成**autoload.php**:

```
composer dump-autoload

    Generating autoload files 
```

当你把 autoload.php 的和 index.php 的**放在一起的时候**

```
require __DIR__.'/../vendor/autoload.php'; 
```

您触发了标准 PHP 库(SPL) [函数](http://php.net/manual/en/function.spl-autoload-register.php)`spl_autoload_register(callable $autoloadFunction)`composer 正在使用它来注册自己，以接管运行时自动加载 PHP 文件的责任(大多数请求中使用的类可以预加载)。

```
<?php
// /ComposerAutoloading/vendor/composer/ClassLoader.php

/**
 * Registers this instance as an autoloader.
 *
 * @param bool $prepend Whether to prepend the autoloader or not
 */
public function register($prepend = false)
{
    spl_autoload_register(array($this, 'loadClass'), true, $prepend);
} 
```

哈！有意思。所以，我的所有跨不同命名空间的类现在都将自动加载了？让我们看看。

```
php index.php

Fatal error:  Uncaught Error: Class 'Santa' not found 
```

该死，圣诞老人还是没找到。这太糟糕了。为什么？

注册的**类加载器**执行下面的`loadClass($class)`方法(为了简单起见，进行了简化和调整):

```
<?php

// /ComposerAutoloading/vendor/composer/ClassLoader.php

/**
 * Loads the given class or interface.
 *
 * @param  string    $class The name of the class
 * @return bool|null True if loaded, null otherwise
 */
public function loadClass($class)
{
    if ($file = $this->findFile($class)) {
        includeFile($file);

        return true;
    }
}

/**
 * Finds the path to the file where the class is defined.
 *
 * @param string $class The name of the class
 *
 * @return string|false The path if found, false otherwise
 */
public function findFile($class)
{
    // class map lookup
    if (isset($this->classMap[$class])) {
        return $this->classMap[$class];
    }

    if (null !== $this->apcuPrefix) {
        $file = apcu_fetch($this->apcuPrefix.$class, $hit);
        if ($hit) {
            return $file;
        }
    }

    // PSR-4 lookup
    $logicalPathPsr4 = strtr($class, '\\', DIRECTORY_SEPARATOR) . $ext;

    $first = $class[0];
    if (isset($this->prefixLengthsPsr4[$first])) {
        ...
    }

    // PSR-4 fallback dirs
    foreach ($this->fallbackDirsPsr4 as $dir) {
        if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr4)) {
            return $file;
        }
    }

    // PSR-0 lookup
    if (false !== $pos = strrpos($class, '\\')) {
        // namespaced class name
        $logicalPathPsr0 = substr($logicalPathPsr4, 0, $pos + 1)
            . strtr(substr($logicalPathPsr4, $pos + 1), '_', DIRECTORY_SEPARATOR);
    } else {
        // PEAR-like class name
        $logicalPathPsr0 = strtr($class, '_', DIRECTORY_SEPARATOR) . $ext;
    }

    if (isset($this->prefixesPsr0[$first])) {
        foreach ($this->prefixesPsr0[$first] as $prefix => $dirs) {
            if (0 === strpos($class, $prefix)) {
                foreach ($dirs as $dir) {
                    if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr0)) {
                        return $file;
                    }
                }
            }
        }
    }

    // PSR-0 fallback dirs
    foreach ($this->fallbackDirsPsr0 as $dir) {
        if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr0)) {
            return $file;
        }
    }

    // PSR-0 include paths.
    if ($this->useIncludePath && $file = stream_resolve_include_path($logicalPathPsr0)) {
        return $file;
    }

    return $file;
} 
```

长话短说。Composer 检查不同类型的存储，试图找到您的圣诞老人类，显然是出于性能原因，按最快的访问顺序排列。

1.  在内存中 **classMap 数组**
2.  APCU 高速缓存
3.  使用 PSR-0 和 PSR-4 标准的磁盘
4.  使用“梨形类名”的磁盘

如果它经历了这么多麻烦，为什么它找不到我的圣诞老人课？我是说，找到圣诞老人有多难...

因为不是卡巴斯基杀毒！它只检查您配置它检查的地方。

在哪里可以配置？正确，旅程从哪里开始，在 **composer.json** 中。

您可以在 classMap 属性中直接指定特定的类(当没有定义名称空间、清晰的目录结构时很有用):

```
{
    "name": "enterprise-php/composer-autoloading",
    "description": "An example how Composer works behind the scenes.",
    "type": "project",
    "authors": [
        {
            "name": "Lukas Lukac",
            "email": "services@trki.sk"
        }
    ],
    "require": {},
    "autoload": {
        "classmap": [
            "src/Christmas/Santa.php"
        ]
    }
} 
```

运行`composer dump-autoload`到一个新的散列文件后会产生什么结果:

```
<?php

// /ComposerAutoloading/vendor/composer/autoload_classmap.php
// autoload_classmap.php @generated by Composer

$vendorDir = dirname(dirname(__FILE__));
$baseDir = dirname($vendorDir);

return array(
    'Christmas\\Santa' => $baseDir . '/src/Christmas/Santa.php',
); 
```

因此，Composer 类查找将非常简单:

```
<?php

public function findFile($class)
{
    // class map lookup
    if (isset($this->classMap[$class])) {
        return $this->classMap[$class];
    } 
```

或者。您可以定义一个更宽泛的 **PSR-4** 标准规则，其可读性为:

*每次你试图找到一个以“Christmas”开头的**类**和**命名空间**，请查看“src/Christmas”目录。*

```
{
    "name": "enterprise-php/composer-autoloading",
    "description": "An example how Composer works behind the scenes.",
    "type": "project",
    "authors": [
        {
            "name": "Lukas Lukac",
            "email": "services@trki.sk"
        }
    ],
    "require": {},
    "autoload": {
        "psr-4": {
            "Christmas\\": "src/Christmas"
        }
    }
} 
```

再一次，在运行`composer dump-autoload`之后，你会得到一个新的类似散列的结构化文件 **autoload_psr4.php** :

```
<?php

// /ComposerAutoloading/vendor/composer/autoload_psr4.php

// autoload_psr4.php @generated by Composer

$vendorDir = dirname(dirname(__FILE__));
$baseDir = dirname($vendorDir);

return array(
    'Christmas\\' => array($baseDir . '/src/Christmas'),
); 
```

不管怎样，运行`php index.php`的输出将会是，我也祝你:

> 圣诞快乐&写 HQ 代码！

#### 总结

Composer 主要是一个依赖项管理器，但是它做了一件了不起的工作，使用它的**ClassLoader.php**注册为一个文件加载器，使用 SPL [函数](http://php<br>%0A.net/manual/en/function.spl-autoload-register.php)自动加载内部、外部(库、依赖项)PHP 类。

Composer 在后台根据您在 **composer.json** 中的设置，使用 [PSR-4](www.php-fig.org/psr/psr-4/) 和 [PSR-0](www.php-fig.org/psr/psr-0/) 命名标准找到文件后，执行 [include](http://php<br>%0A.net/manual/en/function.include.php) 语句。

如果您想进一步研究代码，您可以在这个 GitHub [目录](https://github.com/EnchanterIO/enterprise-level-php/tree/master/examples/ComposerAutoloading)中找到它。

感谢您花时间阅读我关于**企业级 PHP** 的第一篇技术文章。

我也很高兴地宣布，我为致力于软件质量和可维护代码的初级、高级 PHP 开发人员开设的课程开始了！！！

这是我的第一个项目发布，我的推特粉丝很少，所以如果你能转发并传播这个消息，我会非常高兴。

[阅读课程详情！](https://enterprise-level-php.com/)