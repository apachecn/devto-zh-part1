# PHP 包管理历史

> 原文：<https://dev.to/coolgoose/php-package-management-history-59c3>

阅读伟大的文章[现代 Javascript 为恐龙解释](https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70)让我想到如果我们看看 PHP 的包管理和自动加载的历史会是什么样子。

很久以前，在一个遥远的星系中，PHP 5 发布了。与 PHP 4 相比，它有了许多改进(同时保持了向后兼容性)，但是仍然缺少一个适当的方法来包含/加载来自其他文件的包。

在 php 中加载其他文件的标准方法是*包含*或*要求*文件。框架、CMS 和任何其他平台都在手工包含软件包，这使得它变得相对混乱。
这是 phpBB 3.0 的一个小片段(2008-12-13)。

```
<?php
// Include files
require($phpbb_root_path . 'includes/acm/acm_' . $acm_type . '.' . $phpEx);
require($phpbb_root_path . 'includes/cache.' . $phpEx);
require($phpbb_root_path . 'includes/template.' . $phpEx);
require($phpbb_root_path . 'includes/session.' . $phpEx);
require($phpbb_root_path . 'includes/auth.' . $phpEx);
require($phpbb_root_path . 'includes/functions.' . $phpEx);
require($phpbb_root_path . 'includes/functions_content.' . $phpEx);
require($phpbb_root_path . 'includes/constants.' . $phpEx);
require($phpbb_root_path . 'includes/db/' . $dbms . '.' . $phpEx);
require($phpbb_root_path . 'includes/utf/utf_tools.' . $phpEx); 
```

Enter fullscreen mode Exit fullscreen mode

当 PHP 5.1 出现时，它引入了 [spl_autoload_register](http://php.net/manual/ro/function.spl-autoload-register.php) 来定义文件加载的规则。这大大简化了上述情况，并提高了可维护性。

这是 Kohana 3.0 的一个小片段(~2011-02-10)

```
<?php
/**
 * Enable the Kohana auto-loader.
 *
 * @see  http://kohanaframework.org/guide/using.autoloading
 * @see  http://php.net/spl_autoload_register
 */
spl_autoload_register(array('Kohana', 'auto_load'));
...
try
{
    // Transform the class name into a path
    $file = str_replace('_', '/', strtolower($class));
    if ($path = Kohana::find_file('classes', $file))
    {
        // Load the class file
        require $path;
        // Class has been found
        return TRUE;
    }
    // Class is not in the filesystem
    return FALSE;
}
catch (Exception $e)
{
    Kohana::exception_handler($e);
    die;
} 
```

Enter fullscreen mode Exit fullscreen mode

提醒你，我们仍然有一些问题。

1.  对于如何命名文件和文件夹以利用自动加载功能，没有正式的定义。
2.  没有软件包管理器可以简化软件包的安装。
3.  当时的 PHP 没有名称空间设置，从其他包导入很容易产生名称冲突问题(PHP 5.3 引入了名称空间)。

## 包命名

为了解决包的命名问题，第一个社区创建的包命名标准是 [PEAR 编码标准](https://pear.php.net/manual/en/standards.naming.php)，随后是 [ZEND 编码标准](https://framework.zend.com/manual/1.12/en/coding-standard.naming-conventions.html)

解释非常简单:
类名只能包含字母数字字符。类名中允许使用数字，但大多数情况下不鼓励使用数字。下划线只能代替路径分隔符；文件名`Zend/Db/Table.php`必须映射到类名`Zend_Db_Table`。

从那以后，随着 PHP 5.3 和名称空间的发布，事情在 PHP 工作组(现在称为 [PHP-FIG](http://www.php-fig.org/) )的社区领导下取得了进展。

最初的标准是 [PSR-0](http://www.php-fig.org/psr/psr-0/) (现已废弃)。它允许向后兼容 PEAR/ZEND 标准，规定每个`_`字符被转换成一个`DIRECTORY_SEPARATOR`。

下面是一些例子:

```
 \Symfony\Core\Request => /path/to/project/lib/vendor/Symfony/Core/Request.php
    \Zend\Acl             => /path/to/project/lib/vendor/Zend/Acl.php
    \Zend\Mail\Message    => /path/to/project/lib/vendor/Zend/Mail/Message.php 
```

Enter fullscreen mode Exit fullscreen mode

来自社区的最新自动装弹标准是 [PSR-4](http://www.php-fig.org/psr/psr-4/) 。
为了减少`/really/long/paths/that/need/to/be/respected`的情况，简化了规则。这也消除了将`_`作为`DIRECTORY_SEPARATOR`的规则，允许更自然的文件命名。

| 完全限定的类名 | 名称空间前缀 | 基本目录 | 结果文件路径 |
| --- | --- | --- | --- |
| \Acme\Log\Writer\File_Writer | Acme\Log\Writer | /acme-log-writer/lib/ | /acme-log-writer/lib/File _ writer . PHP |
| \ Aura \ Web \响应\状态 | Aura\Web | /path/to/aura-web/src/ | /path/to/aura-web/src/Response/status . PHP |

## 套餐管理

关于包管理 [PEAR](https://pear.php.net/) 发布于 2003 年。Imho PEAR 的主要目标是安装系统包和全局库，它不太适合安装本地应用程序包。不幸的是，它没有获得 PHP 框架和 CMS 的很多支持，它仍然主要用于处理全局包和扩展。

[Composer](https://getcomposer.org/) 诞生于 2011 年，是 PEAR 的一个更现代的替代品，专注于应用。它建立在社区接受 PSR-0 作为自动加载器标准的基础上，并促进了一种更好、更智能的处理应用程序依赖性的方式。在 https://packagist.org/的[上也可以很容易地搜索到软件包，大多数 PHP 社区都支持它。这和 PHP FIG 标准一起降低了项目间的重复工作。](https://packagist.org/)

也就是说，由于社区的努力工作，PHP 在最近几年有了很大的发展。像 PHP-FIG、Composer 和 PHP 7 的发布这样的倡议使得新人编写可维护的 PHP 代码变得更加容易，并有望减少 PHP 的耻辱。我向没有接触过现代 PHP 的人推荐正确阅读 PHP。