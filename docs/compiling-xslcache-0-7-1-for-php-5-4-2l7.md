# 为 PHP 5.4 编译 xslcache 0.7.1

> 原文：<https://dev.to/msanford/compiling-xslcache-0-7-1-for-php-5-4-2l7>

我们在工作中大量使用 xslt(解析 php 控制器生成的 xml 数据)，所以 [xslcache pecl 模块](http://pecl.php.net/package/xslcache/0.7.1)对于我们的基础设施来说非常重要。当然，如果你用的是 PHP 5.4 或以上版本，像很多 pecl 模块一样，[就破](https://bugs.php.net/bug.php?id=62856)。下面是如何让它工作。

目前，在 PHP >= 5.4 的情况下尝试 sudo pecl install xslcache-beta 会在 make 中抛出以下错误:

```
/tmp/pear/temp/xslcache/php\_xsl.c: In function 'xslcache\_objects\_new': /tmp/pear/temp/xslcache/php\_xsl.c:211:52: error: 'zend\_class\_entry' has no member named 'default\_properties' make: \*\*\* [php\_xsl.lo] Error 1 ERROR: `make' failed 
```

Enter fullscreen mode Exit fullscreen mode

### 下载 xslcache

从 pecl svn 存储库中下载原始源代码，即 5.4 补丁，并手动打补丁:

```
svn checkout [http://svn.php.net/repository/pecl/xslcache/trunk](http://svn.php.net/repository/pecl/xslcache/trunk) xslcache cd xslcache wget -O php\_xsl.c.patch 'https://bugs.php.net/patch-display.php?bug\_id=62856&patch=xslcache-php5.4-compat&revision=1362641549&download=1' patch \< php\_xsl.c.patch 
```

Enter fullscreen mode Exit fullscreen mode

### 制作和安装

对于大多数 linux 软件来说，这是一个非常简单的 make 过程(您可能需要调整 xslcache.ini 的位置):

```
phpize ./configure make make test sudo make install sudo nano /etc/php5/cgi/conf.d/xslcache.ini #add "extension=xslcache.so" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您进行测试，第十次可能会失败，结果如下:

```
=================================================================== FAILED TEST SUMMARY 
------------------------------------------------------------------- Test 10: EXSLT Support [tests/xslt010.phpt] =================================================================== 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用 XSLT 2.0，您可能不需要来担心这个问题。

### 确认 xslcache 已安装并启用

既然我们已经在命令行中，让我们检查一下:

```
php -i | grep XSLCACHE 
```

Enter fullscreen mode Exit fullscreen mode

希望您会看到下面一行:

```
XSLCACHE =\> enabled 
```

Enter fullscreen mode Exit fullscreen mode

此外，您应该能够实际知道它正在做它的工作，因为您的基准应该提高。

*原载于 2013 年 3 月 22 日 michaelsanford.com***。**