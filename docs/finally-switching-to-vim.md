# 最终切换到 vim

> 原文：<https://dev.to/peterfication/finally-switching-to-vim>

这篇文章只是我在切换到 vim 时发现的一些有用的东西。因此，当有人因为他或她想要切换到 vim 而寻求帮助时，我只是将这篇文章的链接传给他或她。本文中的一些链接还描述了为什么应该切换到 vim。所以我不会在这里讨论这个。

* * *

在我开始使用 vim 之前，我认为转换只是一个 2-4 周的低效率和学习 vim 的时间框架，然后我会把它都设置好，我习惯了 vim 的做事风格。但是后来我意识到转到 vim 更像是一个漫长而持续的旅程。

关于 vim 最重要的一点是，你不能只是从一份完整的 vim 指南开始，然后就万事大吉了。这是一个不断学习新事物(并被其淹没)的过程。开始时的秘诀是给自己一些时间来适应和理解 vim 概念背后的智慧。

* * *

我建议从观看 Mike Coutermash 对 vim 的介绍开始。他很好地描述了我从 vim 开始时的经历。这里还有他的[博客文章](https://mikecoutermarsh.com/learning-vim-in-a-week/)和[演讲幻灯片](https://mikecoutermarsh.com/boston-vim-learning-vim-in-a-week/)。

为了获得第一印象[，我开始了互动在线教程](http://www.openvim.com/)。本教程为 vim 的原理提供了一个非常好的交互式介绍。[这是一本很好的关于为什么 vim 的读物。](http://www.viemu.com/a-why-vi-vim.html)

在那之后，我意识到我需要所有这些运动命令的列表。这里有一张备忘单，从开始。或者更好的是打印出一份更完整的 vim 备忘单:[这个](https://rumorscity.com/wp-content/uploads/2014/08/10-Best-VIM-Cheat-Sheet-02.jpg)或者[这个](http://michael.peopleofhonoronly.com/vim/vim_cheat_sheet_for_programmers_print.png)。vim wiki 有一个入门页面，我觉得非常有用。

为了训练基本动作，你可以开始玩 [VIM Adventures](https://vim-adventures.com/) 。

这个 Github 库为 vim 提供了一个很长的指南。

这里有 68 个关于维姆和[的免费截屏，还有两个免费的和其他付费的](https://thoughtbot.com/upcase/onramp-to-vim)。

你可能也想看看 [neovim](https://neovim.io/) 。

Vim 有一个非常丰富的插件生态系统。安装插件有多种方式。我从[病原体. vim](https://github.com/tpope/vim-pathogen) 开始，还可以。但是我的同事向我展示了 [vim-plug](https://github.com/junegunn/vim-plug) 的优点，当他们使用 vim-plug 时，我也换了。

然后你必须决定使用哪些插件(和不使用😉)以及如何定制您的设置和键映射。作为一个起点，我会列出一些可能有人会看的插件。这里最重要的是:通读每个插件的文档，决定你是否需要它！

以下是一些我认为对开始有用的插件:

*   [我来了-敏感](https://github.com/tpope/vim-sensible)
*   [fzf](https://github.com/junegunn/fzf)
*   [vim-surround](https://github.com/tpope/vim-surround)
*   [vim-gitgutter](https://github.com/airblade/vim-gitgutter)
*   [nerdtree](https://github.com/scrooloose/nerdtree)

每种编程语言通常都有自己的 vim 插件。

最后，[从头开始玩 vimgolf](http://www.vimgolf.com/) 。开始永远不会太早(我有过这种想法)。看看其他的解决方案，试着理解他们做了什么。

* * *

服用 vim 一周后，我的效率不如服用 RubyMine。如果我真的需要快速完成某件事，我仍然会打开 RubyMine。但是现在大部分时间我都在使用 vim，我喜欢使用它工作。再过两周 RubyMine 就关门了。我最怀念的是 RubyMine 令人敬畏的多光标实现。但是我已经被告知[在 vim 中，你做事情是不同的](https://medium.com/@schtoeffel/you-don-t-need-more-than-one-cursor-in-vim-2c44117d51db)😉

* * *

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*