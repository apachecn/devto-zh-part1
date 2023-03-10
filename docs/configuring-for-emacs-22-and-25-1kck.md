# 为 Emacs 22 和 25 进行配置

> 原文：<https://dev.to/gonsie/configuring-for-emacs-22-and-25-1kck>

我从事高性能计算(HPC)工作。这意味着我在世界上一些最大最好的超级计算机上工作。这也意味着我在 5 年或 10 年前的一些最大和最好的超级计算机上工作。

最近，我发现自己在一台装有 Emacs 22 的机器上。这篇文章记录了我必须对我的 Emacs 配置文件进行的一些更改，以确保它们可以用于我可能遇到的任何 Emacs 版本。

## 旧的初始化文件设置

自从我重新开始使用 Emacs(来自 [SublimeText](https://dev.to/gonsie/converting-from-sublime-text-to-emacs-1ajj) ，我一直在使用一些配置文件。我希望我的`.emacs`是相当普通和高水平的。这将调用一个`init.el`文件，其中有一堆设置和包。我还把一堆特定于机器的配置放在一个`.emacs-custom.el`文件中。

看 Emacs 22，我觉得没有对`package.el`的支持。我可以使用旧版本，但快速搜索后，我只能找到 Emacs 23 的快照。实际上，我不需要完整的 Emacs 包支持，我只想确保我的基本设置是打开的(我喜欢我的颜色，讨厌自动保存文件)。

## 新方案

新方案有四个文件，其中三个是从根`.emacs`文件调用的。

### `.emacs`

这个文件包含所有普通的 Emacs 22 设置。有些包，比如 autopair 或 org，曾经包含在 Emacs 发行版中，但是没有保证。也就是说，这个文件中的设置对于所有版本的 Emacs 都是安全的。

### `.emacs-custom.el`

该文件包含任何特定于机器的设置(如 ispell 路径)或自定义设置。注意，在新机器上设置时，你必须手动`touch`这个文件，否则 Emacs 会报错。

### `init.el`

这个文件包含了所有与包有关的东西。它有 MELPA 的设置，然后使用-package 做其他事情。我真的很喜欢这个文件的简洁和单一的目的。

### `theme.el`

这个文件包含了我的颜色主题的所有设置。对于 Emacs 的所有版本，这个文件也必须是正确的(即不抛出错误)。任何特定于包的颜色都必须得到适当的保护，以防包没有被加载。

对于我的特殊设置，我只能做到这一点，因为我在我的 dotfiles 发行版中包含了`color-theme`包/elisp 文件。颜色是出了名的棘手，所以我有一些工作，我不会改变它。

## 提示

这里有一些我用来让一切工作的片段。

### 检查 Emacs 版本

只能从 Emacs 25 或更高版本中调用`init.el`。我的`init.el`可能也适用于 Emacs 24，但是我现在就把它放在一边。

```
(when (< 24 emacs-major-version)
  (load "~/.config/emacs/init.el")) 
```

Enter fullscreen mode Exit fullscreen mode

### 检查包裹

某些特定于软件包的颜色只能在加载软件包时设置。我想这可以通过 use-package 来完成，但是我现在有 org 在那之外。

```
(when (require 'org nil 'noerror)
  (set-face-attribute 'org-table t :foreground "#536fd6")) 
```

Enter fullscreen mode Exit fullscreen mode

### 包初始化

因为我的包都加载在`init.el`文件中(并且该文件只为特定的 Emacs 版本加载)，所以我将 MELPA 设置和`(package-initialize)`调用放在那里。这导致`package.el`抱怨并自动调用`.emacs`文件中的`(package-initalize)`。因此，要使用这样的设置，我需要在我的`.emacs`文件的开头添加一个注释。

```
;; package.el will check for this comment; it's actually called from init.el
;; (package-initialize) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我认为这个新计划相当巧妙。无论我在什么机器上工作，我都能得到我喜欢的颜色和设置。另外，当我启动 Emacs 时，没有投诉/警告/错误。