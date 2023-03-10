# 命令行效率提升 100%

> 原文：<https://dev.to/sobolevn/instant-100-command-line-productivity-boost>

富有成效很有趣。

有很多领域可以提高你的生产力。今天，我将分享一些命令行提示和技巧，让您的生活更轻松。

## TLDR

我的完整设置包括本文中讨论的所有内容，甚至更多。来看看:[https://github.com/sobolevn/dotfiles](https://github.com/sobolevn/dotfiles)

## 外壳

使用一个好的、有帮助的、稳定的 shell 是提高命令行效率的关键。虽然有很多选择，但我更喜欢`zsh`加上`oh-my-zsh`。令人惊讶的原因有几个:

*   自动完成几乎所有内容
*   大量插件
*   真正有帮助且可定制的`PROMPT`

您可以按照以下步骤安装此安装程序:

1.  安装`zsh`:[https://github . com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH)
2.  安装`oh-my-zsh`:[http://ohmyz.sh/](http://ohmyz.sh/)
3.  选择可能对你有用的插件:[https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)

你可能还想调整你的设置来关闭区分大小写的自动完成功能。或者改变你的[历史如何运作](https://github.com/sobolevn/dotfiles/blob/master/config/zshrc#L24)。

就是这样。你将立即获得+50%的生产力。现在尽可能多地点击 tab 键！

## 主题

选择主题也很重要，因为你总是会看到它。它必须既实用又美观。我也喜欢极简主义的主题，因为它不包含很多视觉噪音和无用的信息。

你的主题应该向你展示:

*   当前文件夹
*   当前分支
*   当前存储库状态:干净或脏
*   错误代码(如果有)

我也更喜欢我的主题在新的一行有新的命令，这样就有足够的空间来读写它。

我个人用 [`sobole`](https://github.com/sobolevn/sobole-zsh-theme) 。看起来很棒。它有两种模式。

光线:

[![sobole.zsh-theme](img/2371697d6b86ff0554d371f973ad77cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lz_uthoR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/sobolevn/sobole-zsh-theme/master/showcases/env-and-user.png)

和黑暗:

[![sobole.zsh-theme](img/e958245417ae052608c885b9c5df49c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4o6hZwL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/sobolevn/sobole-zsh-theme/master/showcases/dark-mode.png)

再次获得+15%的提升。一个很棒的主题。

## 语法高亮显示

对我来说，从我的 shell 中获得足够的视觉信息来做出正确的决定是非常重要的。比如“这个命令的名字中有错别字吗”或者“这个命令中有成对的作用域吗”？我真的一直在做 tpyos。

所以， [`zsh-syntax-highlighting`](https://github.com/zsh-users/zsh-syntax-highlighting) 对我来说是一个重大发现。它有合理的默认值，但是你可以[改变你想要的任何东西](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/docs/highlighters.md)。

这些步骤给我们带来了额外的+5%。

## 处理文件

我经常在我的目录里旅行。非常喜欢*。我在那里做所有的事情:*

 **   向后和向前导航
*   列出文件和目录
*   打印文件内容

我更喜欢使用 [`z`](https://github.com/rupa/z) 来导航到我已经去过的文件夹。这个工具太棒了。它用“频率”的方法把你的“T1”变成“T2”。真好看！

打印文件时，您通常需要了解几件事情:

*   文件名
*   许可
*   物主
*   文件的 git 状态
*   修改日期
*   人类可读形式的尺寸

你也可能知道默认情况下显示隐藏的文件。所以，我用 [`exa`](https://github.com/ogham/exa) 来代替标准`ls`。为什么？因为它有很多默认启用的东西:

[![exa](img/567f0f8275fb0337819d3635881ffd64.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n_YCO9Hj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ogham/exa/master/screenshots.png)

为了打印文件内容，我使用标准的`cat`,或者如果我想查看正确的语法高亮，我使用自定义别名:

```
# exa:
alias la="exa -abghl --git --color=automatic"

# `cat` with beautiful colors. requires: pip install -U Pygments
alias c='pygmentize -O style=borland -f console256 -g' 
```

现在你已经掌握了导航。获得+15%的生产力提升。

## 搜索

当在应用程序的源代码中搜索时，默认情况下，您不希望在结果中包含像`node_modules`或`bower_components`这样的文件夹。你也希望你的搜索快速而流畅。

这里有一个很好的替代内置搜索的方法: [`the_silver_searcher`](https://github.com/ggreer/the_silver_searcher) 。

它是用纯`C`编写的，并且使用了很多聪明的逻辑来快速工作。

在`history`中用`ctrl` + `R`进行[反向搜索](https://unix.stackexchange.com/questions/73498/how-to-cycle-through-reverse-i-search-in-bash)非常有用。但是你有没有发现自己处在一个我能完全记住一个命令的情况下？如果有一种工具可以让这种搜索变得更好，支持模糊搜索和漂亮的用户界面，会怎么样？

实际上，有这样一个工具。它叫做`fzf`:

[![fzf](img/f36fee89082835a599ae511ea254ee58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hykHvwjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erts5tffgo5i0rpi8q3r.png)

它可以用来模糊查找任何东西，不仅仅是历史。但是需要[一些配置](https://github.com/sobolevn/dotfiles/blob/master/shell/.external#L19)。

你现在是一名搜索忍者，拥有+15%的生产力加成。

## 进一步阅读

使用更好的 CLI:[https://dev.to/sobolevn/using-better-clis-6o8](https://dev.to/sobolevn/using-better-clis-6o8)

## 结论

遵循这些简单的步骤，您可以极大地提高您的命令行效率，比如+100%(数字是近似值)。

我将在下一篇文章中介绍其他工具和技巧。

你喜欢阅读软件开发的最新趋势吗？在 Medium 上订阅我们的博客:[https://medium.com/wemake-services](https://medium.com/wemake-services)*