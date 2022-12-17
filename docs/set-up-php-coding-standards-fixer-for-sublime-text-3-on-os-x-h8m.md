# 在 OS X 上为 Sublime Text 3 设置 PHP 编码标准固定器

> 原文：<https://dev.to/mariordev/set-up-php-coding-standards-fixer-for-sublime-text-3-on-os-x-h8m>

通过运行一个简单的命令来自动格式化您的 PHP/Laravel 代码以遵循 PSR-1 和 PSR-2 标准，您可以节省很多时间。让我向你展示如何在 OS X 上安装 Sublime Text 3。

## 安装 php-cs-fixer

您的系统上必须至少有`PHP 5.3.6`。用`php --version`检查。

运行以下命令下载 php-cs-fixer:

```
cd ~/Downloads
wget http://get.sensiolabs.org/php-cs-fixer.phar -O php-cs-fixer 
```

如果你没有安装`wget`，[按照这些说明安装](https://coolestguidesontheplanet.com/install-and-configure-wget-on-os-x/)。检查[这个页面](http://ftp.gnu.org/gnu/wget/)，这样你就可以得到最新的版本。

或者使用`curl` :

```
curl http://get.sensiolabs.org/php-cs-fixer.phar -o php-cs-fixer 
```

如果您喜欢不同的下载方式，如 composer 或 homebrew，[请查看这些说明](http://cs.sensiolabs.org/)。

现在你已经下载了 php-cs-fixer，让它可执行:

```
sudo chmod a+x php-cs-fixer 
```

将其移动到全球位置:

```
sudo mv php-cs-fixer /usr/local/bin/php-cs-fixer 
```

通过运行
检查它是否工作

```
php-cs-fixer 
```

如果一切正常，您应该会看到版本和使用信息。

## 在 Sublime Text 3 中配置构建命令

*   开放崇高的文本
*   进入工具>构建系统>新建构建系统…
*   输入以下命令并保存:

```
{
    "shell_cmd": "php-cs-fixer fix $file --level=psr2"
} 
```

就是这样。当您运行这个命令时，php-cs-fixer 会将 psr-2 标准应用到您打开的当前文件中。

按下`Command + B`运行命令。

干杯！