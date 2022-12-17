# 扫描您的 PHP 项目中的 CVE

> 原文：<https://dev.to/gabeguz/scan-your-php-project-for-cves>

我最近发现了一个非常好的工具，用于扫描项目的安全问题:[https://security.sensiolabs.org/](https://security.sensiolabs.org/)。你将需要使用[https://getcomposer.org/](https://getcomposer.org/)来利用它，但是如果你是，那么它就像下载工具并对你的`composer.lock`文件运行它一样简单，就像这样:

```
$ php security-checker security:check /path/to/composer.lock 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用的任何库有任何问题，你会收到一条很好的消息，告诉你问题是什么，什么是 CVE，如果有影响这些库的话。我已经为我的团队将这一点连接到 CI 中，所以我们可以确保我们没有发布具有已知安全漏洞的代码。