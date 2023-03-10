# 胜利的 fzf

> 原文：<https://dev.to/dmerejkowsky/fzf-for-the-win>

*原载于[我的博客](https://dmerej.info/blog/post/fzf-for-the-win/)*

上个月第一次听说 [fzf](https://github.com/junegunn/fzf) 。

今天，它已经成为我最喜欢的工具之一，我再也无法想象在没有安装它的电脑上工作。

让我告诉你这是怎么发生的，为什么会发生。

首先，我将描述我在 shell (zsh)和文本编辑器(neovim)中使用的一些不同的工具。

然后，我将向您展示我如何以及为什么用`fzf`替换所有这些工具，以及它如何让我拥有更好的工作流程。

# 黑暗时代(fzf 之前)

[![dark times](img/04cab2af14a5e95bc8b628d8ab4fe80b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cRXKjylj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/dark-times.jpg)

## 在文件夹中查找文件

假设我正试图编辑深埋在`src`文件夹中的`FooManager.java`。

我的 zsh 历史是这样的:

```
$ find -name foo
# Oups, forgot to put the dot
$ find . -name foo
# Oups, the file is not named 'foo' exactly, I need
# to add globs
$ find . -name *foo*
# Oups, I need to add quotes to prevent the shell
# from expanding the globs:
$ find . -name "*foo*"
# Oups, find is case-sensitive by default, so I need
# `-iname` instead of `-name`
$ find . -iname "*foo*"
src/main/java/com/example/foo/FooManager.java
# Now I can copy/paste the file path and
# edit the file in vim:
$ vi 'shift-ctrl-v' 
```

Enter fullscreen mode Exit fullscreen mode

唷！对于这样一个基本任务来说，击键次数太多了！

## 浏览 zsh 历史

### 上上下下

我会使用`CTRL-N`和`CTRL-P`在历史中上下导航。

例如，如果我输入了下面的命令列表:

```
$ ls foo
$ ssh server1
$ ls bar
$ ssh server2 
```

Enter fullscreen mode Exit fullscreen mode

我可以按下`CTRL-P`三次得到`ssh server1`，然后按下`CTRL-N`得到`ls bar`。

### 使用箭头键

我还会用另一个技巧来对付 zsh。您可以告诉 zsh 只显示以与当前行相同的字符开头的命令，而不是按时间顺序列出所有命令。

所以我在我的`~/.zshrc` :
里有这样的东西

```
bindkey '^[[A' history-beginning-search-backward
bindkey '^[[B' history-beginning-search-forward 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我可以做:

```
$ ssh <up key> 
```

Enter fullscreen mode Exit fullscreen mode

然后继续按“向上”,直到找到正确的服务器。如果我走得太远，我可以直接按“下”键。

### 寻找特定的命令

有时我想重新输入一个命令，但我知道它在历史列表的很下方。或者，我记得命令的一部分，但不记得它是如何开始的。

因此，与其多次点击`CTRL-P`,我不如使用`CTRL-R`。

但是恰当地使用`CTRL-R`并不明显。对于我的`zsh`配置，我必须:

*   类型`CTRL-R`
*   输入模式
*   打错字
*   使用`backspace`修复错别字
*   多次点击`CTRL-R`
*   意识到我走得太远了
*   小心地键入`backspace` *一次*以进入前一场比赛
*   按回车键

此外，有时我会收到可怕的消息，这通常意味着我必须重新开始。

## In neovim

### 列出缓冲区

在`neovim`中，您可以使用`:list`列出所有打开的缓冲区。

有这样显示的:

```
 1 %a + foo.txt
  3 #h   bar.txt
 10  a   baz.txt 
```

Enter fullscreen mode Exit fullscreen mode

每个缓冲区都有一个数字。数字和名称之间的列包含诸如缓冲区是活动的还是隐藏的、是否被修改等等信息。

`#`符号表示“备用”缓冲区，也就是说，如果你按下`:b#`键，你将从备用缓冲区回到当前缓冲区。

您可以使用`:b<num>`切换到缓冲区，其中`<num>`是缓冲区编号。

您还可以在`:b`后使用制表符结束来列出包含某个模式的所有缓冲区名称。

因此，要打开缓冲区“baz.txt ”,我可以键入:

```
:b bar
'tab'
" nope, I don't want bar.txt
'tab'
" ok, I got baz.txt
'enter' 
```

Enter fullscreen mode Exit fullscreen mode

这非常有效，除了有时候我会得到太多的匹配，然后我会花很长时间点击`<tab>`来找到我想要的缓冲区。

### 浏览旧文件

neovim 会保存一份您编辑过的所有文件名的列表。

您可以使用`:oldfiles`显示列表。

看起来是这样的:

```
1: /home/dmerej/foo.txt
2: /home/dmerej/bar.txt
3: /home/dmerej/src/spam/eggs.py
-- More -- 
```

Enter fullscreen mode Exit fullscreen mode

你必须按下`enter`才能看到列表的其余部分，在你按下`enter`之后，就没有办法返回了。

你也不能在列表中搜索。

而如果你想打开其中一个文件，你就得使用`:browse oldfiles`，仔细记住它的索引，当出现提示时，键入数字，按下
`enter`。

这有点乏味，而您想要的只是一个简单的“从最近使用的中选择”功能，几乎所有其他文本编辑器都有这个功能:/

# 寻找图案

[![patterns](img/52d29431a332994fce652cb378116e12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a5DhlFhA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/patterns.jpg)

这是一个非常通用的技术，你可以用它来改善你的工作流程。寻找一种模式，然后找到一种可以在任何地方重复使用的处理方法。

我刚才展示的所有工具的模式是什么？

在每一种情况下(从浏览 neovim 缓冲区或试图在`zsh`历史中找到一些东西)，我们都有一个列表(或多或少排序)，我们想从列表中选择*一个且仅一个*元素。

我们非常关心交互性:这意味着它应该很容易从一些错误中恢复，例如以错误的顺序键入模式中的字母，出现错别字，或者不关心大小写。

这就是`fzf`的用武之地。

# fzf 为胜！

只做一件事，而且做得很好。你应该把它和一个壳管一起使用，就像这样:

```
$ some-source | fzf 
```

Enter fullscreen mode Exit fullscreen mode

这里`fzf`将使用几行文本，显示由`some-source`命令返回的元素列表。(默认为最后一个`n`元素)。

但是当您输入模式来过滤匹配列表时，它会实时更新列表。

它将对匹配的部分进行着色，并允许您输入“模糊”的图案(允许打字错误等)

你可以在[asci NEMA](https://asciinema.org/a/120929)上看到它的运行

请注意:

*   当从`tmux`使用时，您可以使用`fzf-tmux`命令来代替，它将自动在分割面板中显示列表。
*   您可以使用模式中的符号来更改元素的过滤方式:

    *   使用单引号获得精确匹配
    *   `^foo`:使用插入符号选择以`foo`开头的元素
    *   `foo$`:使用美元选择以`foo`结尾的元素
*   在`fzf`手册页和[维基](https://github.com/junegunn/fzf/wiki)中有大量的信息

*   `fzf`也可以把它本身钩在你的壳的完成机械上。它非常“管用”。详见[文档](https://github.com/junegunn/fzf#fuzzy-completion-for-bash-and-zsh)。

## 安装

```
$ git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
$ ~/.fzf/install 
```

Enter fullscreen mode Exit fullscreen mode

注意`fzf`是用 Go 写的，意思是:

*   它很快
*   它没有依赖关系
*   它是跨平台的
*   它可以很好地处理异步的东西

更准确地说，这意味着 fzf 一启动，*你就可以开始选择，而 fzf 仍然在处理它的输入*。

## fzf 开箱

### zsh 历史

您可以配置`fzf`，以便它在您点击`CTRL-R`时运行。

我个人也告诉`fzf`打箭头键就开始:

```
bindkey '^[[A' fzf-history-widget 
```

Enter fullscreen mode Exit fullscreen mode

我非常习惯于使用箭头键来导航 zsh 历史，因此在这种情况下重写行为是有意义的。

尽管如此，当您想要的只是重新键入之前的命令时，`fzf`还是有点多余，所以我还配置了`CTRL-N`和`CTRL-P`来做“愚蠢的”历史导航:

```
bindkey '^P' up-line-or-history
bindkey '^N' down-line-or-history 
```

Enter fullscreen mode Exit fullscreen mode

### 替换查找

默认情况下，当按下`CTRL-T`，`fzf`将允许递归选择目录中的任何文件名，当您完成选择时，它将在您的当前行后插入路径。

例如，你可以使用:

```
$ vim <ctrl-t>
# Start fzf
# Select Foo.java
# Press enter once, line becomes:
$ vim src/main/java/com/example/foo/Foo.java
# Press enter to edit the file 
```

Enter fullscreen mode Exit fullscreen mode

这比我以前使用的“运行查找并复制/粘贴结果”技术要有效得多:)

## fzf and neovim

默认情况下，`fzf`会在`fzf`可执行文件上安装一个非常简单的包装器。

交互式列表不是显示在终端中，而是显示在一个特殊的 neovim 缓冲区中，但是其余的用法(包括击键)与 shell 版本相同。

我建议您使用 [fzf.vim](https://github.com/junegunn/fzf.vim) ,它为您带来了现成的命令，例如:

*   `:History`，利用`:oldfiles`命令编辑旧文件
*   `:Buffers`，在当前窗口开始编辑缓冲区
*   `:Files`，编辑当前目录下的其中一个文件

安装好插件后，你要做的就是配置一些映射:

```
nnoremap <leader>p :History<CR>
nnoremap <leader>b :Buffers<CR>
nnoremap <leader>t :Files<CR> 
```

Enter fullscreen mode Exit fullscreen mode

`fzf.vim`也允许你使用一点`vimscript` :
来创建你自己的命令

```
command! -nargs=0 MyCmd :call SelectSomething()

function! SelectSomething()
call fzf#run({
 \ 'source': "my-cmd",
 \ 'sink': ":DoSomething"
 \})
endfunction 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了一个名为`MyCmd`的命令，它将调用`SelectSomething()`函数。

`SelectSomething`将依次使用`source`参数的输出作为`fzf`输入，然后将在所选元素上运行`sink`命令。

# 一致性是关键

老实说，我必须说我已经在使用一个名为 [CtrlP](https://github.com/kien/ctrlp.vim) 的模糊查找器了。

但是我决定停止使用，改用`fzf`。

这是因为在我的 shell 和编辑器中使用相同的工具真的很棒。这意味着我只需要学习如何使用和配置它一次。

但这比那更进一步。例如，在我的设置中:

*   `:Files`命令使用 neovim 中的`<leader>t`，shell 的等效命令是`<CTRL-t>`。(同一封信)

*   在`vim-fugitive`中有一个`:Gcd`命令，在我的`.zshrc`中有一个`gcd`命令来做同样的事情:

```
# go to a path relative to the top directory
# of the current git worktree.
function gcd() {
  topdir=$(git rev-parse --show-toplevel)
  if [[ $? -ne 0 ]]; then
    return 1
  fi cd "${topdir}/${1}"
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是巧合。毕竟，如果我在我的编辑器和 shell 中做同样的事情，我希望能够使用类似的按键。

我仍然有一些关于编辑器/shell 关系的事情要说，但这将在另一篇文章中完成。(无耻调侃)

# 结论

这就是我关于`fzf`的故事。这可以归结为一个非常简单的过程:

*   找到工具使用的模式和分数
*   找到一个通用的解决方案来处理你已经发现的模式
*   尝试在任何地方获得一致的配置和映射，以便您更容易输入和记忆。

顺便说一句，如果这让你想起布莱姆·米勒著名的[有效编辑文本的七个习惯](http://moolenaar.net/habits.html)的文章，这也不是巧合:)

干杯！