# ★如何使用自制软件在 MacOS 上从 PHP 7.1 升级到 7.2

> 原文：<https://dev.to/freekmurze/how-to-upgrade-from-php-7-1-to-7-2-on-macos-using-homebrew-2bc9>

[PHP 7.2](http://php.net/manual/en/migration72.new-features.php) 发布快两个月了。我决定等一等，直到兼容 PHP 7.2 的稳定版 [Xdebug](https://xdebug.org/) 发布。随着 Xdebug 2.6 的发布，昨天就发生了这种情况。

我在 MacOS 上用 brew 安装东西。从 PHP 7.1 升级到 7.2 简单得可笑。这些是我执行的步骤:

```
brew tap homebrew/homebrew-php
brew unlink php71
brew install php72 --with-argon2 
brew install php72-xdebug 
```

嘣！搞定了。

如果需要 imagick 或 redis 支持，执行这两个命令:

```
brew install php72-imagick
brew install php72-redis 
```

这就是全部了。

[![terminal](img/620037cb7386ff6a7fb371680c99e352.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3BYMEk7X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/php72/terminal.png)