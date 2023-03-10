# (哦，我的)ZSH Symfony 完成

> 原文：<https://dev.to/jerguslejko/oh-my-zsh-symfony-completion-139>

我向你展示我自己的 Oh My Zsh 插件，它为你最喜欢的工具带来命令行自动完成功能，例如`laravel/artisan`、`laravel/valet`、`composer`或任何其他基于`symfony/console`的命令行工具！

遵循 Github 上的指令，让你的命令行充满活力！

# 背景故事:如何&为什么

我是 [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) 的超级粉丝，也是 Laravel 的超级粉丝。当我切换到 Oh My Zsh 时，我立即爱上了各种工具的“自动完成插件”，如`git`或`brew`。然而，我在日常使用的工具中缺少了这个功能:`artisan`和`composer`。

我已经谷歌了合适的插件，但我并不完全满意我找到的那些。它们要么是过时的(缺少新的命令)，要么只提供固定的命令集(它不会选择你自定义的 artisan 命令)。我决定看看这些插件是如何构造的，并想出一个新的插件来满足我的所有需求。

我的第一个目标是创建一个“Artisan 补全插件”,它将解析`php artisan`的输出，并为我提供全功能的自动补全。我设法让原型在几个小时内工作，我对结果非常满意。然后是打击我！Laravel 的`artisan`是建立在`symfony/console`包之上的。`composer`也是！

我决定推广这个插件来支持任何基于`symfony/console`的工具。这包括`laravel/installer`甚至`laravel/valet`！要激活您喜欢的工具的自动完成功能，只需在`.zshrc`中指定工具的名称。请看一下[Github 库](https://github.com/jerguslejko/zsh-symfony-completion#instalation)以获得进一步的说明。

所以，我们到了。去吧，试试看，让我知道你的想法。谢了。