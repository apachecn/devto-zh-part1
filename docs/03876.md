# PHP-FPM 优化:使用' pm static '实现最大性能

> 原文：<https://dev.to/phpprofi/-php-fpm--pm-static----2g00>

让我们简单地看一下如何更好地配置 PHP-FPM，以实现高吞吐量、低延迟以及更稳定的处理器和内存利用率。大多数缺省的 PHP-FPM 设置都包含一个安装在 T0 中的 PM (process manager)字符串，如果您遇到可用内存问题，还提供了使用 T1 的建议。不过，让我们看一下 php.net 文档，比较两种管理选项，并比较一下我最喜欢的高访问量设置...T2:

http://phpprofi.ru/blogs/post/70 的читать