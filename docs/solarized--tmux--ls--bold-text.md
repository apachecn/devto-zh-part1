# 日晒+ tmux + ls +粗体文本

> 原文：<https://dev.to/zachwhaley/solarized--tmux--ls--bold-text>

我是如何得到了*SolarIzed*、`tmux`、`ls`、**粗体文本**协同工作。

从日光化的`tmux`会话运行`ls`会返回一堆灰色的目录和文件，而不是我们预期的可区分的颜色。

[!["Bright" blue](img/79502ed19cd1e8239026266b154166b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QZ-IkfvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/FqQwFpK.png)

这是因为`ls`对目录和某些文件使用[【亮】](https://en.wikipedia.org/wiki/ANSI_escape_code#Colors)颜色，但是 Solarized 用它的基色覆盖了这些“亮”颜色。

但是我不想要“明亮”的颜色，我想要**粗体**文本。所以我花了一些时间搜索和破解配置文件，最终我得到了我想要的！

`ls`打印带有**粗体文本**ðÿ˜€的彩色目录和文件

首先，我真的不知道为什么ðÿ˜•会这样

# 我的设置

我只在我自己的设置中测试过，所以如果它不适合你，对不起

[![Setup](img/c1c5b9fd26e6976e53ffe79ecf028167.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OaOHFQdZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/g1nSihX.png)

# 日晒雨淋

谢天谢地，这里没有变化。

我只是按照[伊森](http://ethanschoonover.com/solarized)的指示，将我的终端颜色设置为美丽的日晒色。

# tmux

您必须使用`tmux` 2.2 或更高版本。我注意到这在`tmux` 2.1 上不起作用。

```
$ tmux -V
tmux 2.2 
```

Enter fullscreen mode Exit fullscreen mode

我的`TERM`环境变量默认设置为`xterm-256color`。我不确定这是终结者的风格还是 Fedora 的风格，但事实就是如此。

在我的`.tmux.conf`文件中，我将`default-terminal`选项设置为`screen-256color`。

```
set -g default-terminal screen-256color 
```

Enter fullscreen mode Exit fullscreen mode

最后，我在打开`tmux`时使用了`-2`选项来强制`tmux`假设终端使用 256 色。我为`tmux`创建了一个别名，其中包括这个

```
alias tm='tmux -2' 
```

Enter fullscreen mode Exit fullscreen mode

# 粗体文本

我使用`dircolors`在我的主目录中创建了一个`.dir_colors`文件。

```
dircolors -p > ~/.dir_colors 
```

Enter fullscreen mode Exit fullscreen mode

该文件定义了打印文件和目录时`ls`将使用的所有颜色代码。

然后，我使用 [ANSI SGR 参数代码 38](https://en.wikipedia.org/wiki/ANSI_escape_code#CSI_codes) 来指定扩展的 256 种颜色中的颜色。

例如:在 dircolors 中，用于设置目录颜色的配置`DIR`被设置为`1;34`，这导致了一种明亮的(1)蓝色(34)颜色。

```
DIR 1;34 
```

Enter fullscreen mode Exit fullscreen mode

但是，正如我提到的，日晒后的“亮蓝色”实际上并不是蓝色，而是一种灰色色调。

使用`38;5` ANSI 代码，我能够指定**粗体**(1)ANSI 256(38；5)颜色蓝色(4)，不鲜艳！

```
DIR 1;38;5;4 
```

Enter fullscreen mode Exit fullscreen mode

[![Bold blue](img/48bc513d4b4b33c08a689cfb618ab206.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dBhkDynv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/kxQ6tcC.png)

这就对了。

你可以在 [GitHub](https://github.com/zachwhaley/dotfiles/) 上找到我剩下的`.dir_colors`文件，以及我所有的主目录配置文件。

[http://zachwhaleys . website/2017/01/19/solarized+tmux+ls+bold text/](http://zachwhaleys.website/2017/01/19/solarized+tmux+ls+boldtext/)