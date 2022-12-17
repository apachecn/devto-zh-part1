# 从崇高文本到 Emacs 的转换

> 原文：<https://dev.to/gonsie/converting-from-sublime-text-to-emacs-1ajj>

*   [动机](#motivation)
*   [先决条件](#prerequisites)
*   [基础知识](#the-basics)
*   [崇高文字中的极品](#the-best-from-sublime-text)
*   [在 ST 中从未真正奏效的事情](#things-that-never-really-worked-in-st)
*   [组织模式](#org-mode)
*   [结论](#conclusion)

# 动机

直到最近，Sublime Text (ST)一直是我的首选代码编辑器，尤其是在处理项目时。多光标功能完全改变了我的生活，我无法想象没有它们的生活。除此之外，ST 使其他事情变得非常简单:简单的包管理、直接的项目导航、跨系统的简单同步。

在过去的几个月里，我一直和一位 Emacs 专家密切合作，他在整个工作日都使用 org-mode。从一个文件中纠结和导出的能力让我觉得很有意义，我必须尝试一下。我一直是 Emacs 团队的一员，我甚至用 org 语法创建了我的第一个 GitHub read-me。但是，没有人敲门，我没有真正理解组织模式的全部力量，它也没有坚持下去。

在这篇文章中，我试图记录我从 Sublime Text 3 到 Emacs 的转变。主要动机是为了全面的组织模式支持。虽然有一些 ST 包提供了一些 org-mode 语法，但是缺少完整的功能。因此，我想把我在 ST 中学到的一切(所有提高生产率的特性)用 Emacs 复制过来。

在这篇文章中，我不断得出相同的结论:Emacs 支持我想做的奇怪的事情。真的，这个 XKCD 漫画是真的。

设置:我使用一台 Mac 电脑(技术上来说是两台，一台在家里，一台在办公室)，通过自制软件安装了最新的 Emacs。根据 Aaron Bieber 的这篇文章，我主要是通过 GUI 程序使用 Emacs。

# 先决条件

我敢肯定，对于那些刚刚开始使用 Emacs 的人来说，这里有一些资源。在阅读这篇文章的其余部分之前，我将只介绍一些你应该知道的基础知识。

## 假设

您应该了解 Emacs 的基础知识，即:

*   懂和弦，比如`C-x C-c`。
*   获得关于`C-h ?`的帮助
*   用`C-g`退出(通常用于和弦出错)
*   了解如何检查您的 Emacs 环境
    *   `M-x describe-key`
    *   `M-x describe-variable`
    *   `M-x describe-function`(在尝试`C-x M-c butterfly`之前运行此命令)。
*   了解缓冲区和窗口
    *   用`C-x o`在打开的窗口之间切换
    *   用`C-x 0`移除该窗口
    *   将此窗口水平、`C-x 2`或垂直`C-x 3`分成两半。
    *   用`C-x b`切换到另一个缓冲区

了解这些事情会让你走得很远...防止你打碎太多东西。其他的，谷歌一下就知道了！

## 套餐管理

Emacs 支持许多包管理器，但是最流行的是 [MELPA](http://melpa.org/#/) 。查看 MELPA 网站获取最新的[安装说明](http://melpa.org/#/getting-started)。一旦 MELPA 被配置，你可以手动安装软件包:

```
M-x package-install RET package-name 
```

Enter fullscreen mode Exit fullscreen mode

在 MELPA 之上，我使用 [use-package](http://melpa.org/#/use-package) 包来组织我需要的所有包。这对于多台机器之间的同步特别有效。

另一个解决方案可能是[请求包](http://melpa.org/#/req-package)。这是建立在 use-package 之上的，但是我还没有试过。

## 颜色主题

设置终端配色方案实际上是一件非常困难的事情。冲突主要产生于颜色实际应该被指定的地方:后端还是前端。Emacs 应该规定“红色”是什么样子，还是应该终端程序？此外，在 GUI 和终端中获得相同的配色方案可能很棘手。

下面是我初始化 Emacs 颜色主题的代码。它有一个有用的片段是为 GUI Emacs 设置背景颜色(在终端中不应该这样做)。

```
(eval-after-load "color-theme"
  '(progn
     (color-theme-initialize)
     (color-theme-dusk)
     (when (display-graphic-p)
       ;; settings for GUI emacs
       (add-to-list 'default-frame-alist '(background-color . "#282B35"))
       (add-to-list 'default-frame-alist '(foreground-color . "White"))
       (set-frame-font "Inconsolata 18" nil t)))) 
```

Enter fullscreen mode Exit fullscreen mode

我将在另一篇文章中更详细地介绍我的颜色主题。

# 基础知识

设置 Emacs(或任何编辑器)需要一些基本的东西。如果您已经有了一个很棒的 Emacs 配置，就直接跳到前面。

## 机器间同步

多年来，我一直在用 Dropbox 同步 ST 设置和软件包。在我开始为一家不喜欢随意云服务的公司工作之前，这种做法一直很有效。虽然云解决方案也能与 Emacs 一起工作，但我希望我的新设置能更好地支持远程机器。

对于我的常规 UNIX 设置(bash 配置文件等等)，我使用一个[点文件](https://github.com/gonsie/dotfiles) git repo。这个 repo 可以被克隆到任何新机器上，并包含设置说明。它还包括一个`synch.sh`脚本，该脚本做两件事:

1.  对于新的安装，任何现有的点文件将被覆盖保存到一个特殊的文件夹。然后，我所有的点文件被安装并符号链接到我的主目录。
2.  对于现有安装，同步脚本将使存储库中的文件与已安装的文件不同。因此，我在本地所做的任何更改都可以被添加回 repo，并被提交和共享。

特别是对于 Emacs，这就是[使用包](http://melpa.org/#/use-package)的用武之地。当我在一台新机器上启动时，Use-package 会自动地处理安装包。目前，我设置了 3 个单独的 Emacs 相关的点文件:

1.  `.emacs`:这个文件是裸骨。它设置 MELPA 和，并包含设置新机器的说明(例如安装 use-package)。它还调用加载其他两个文件。
2.  这是我所有设置所在的文件。它不一定需要从`.emacs`文件中分离出来，但是现在，它是分离的。我所有的使用包声明都在这个文件中。
3.  `.emacs-custom.el`:这个文件存放了我所有的`M-x customize`设置。这些变量中有许多是特定于机器的，例如，安装了哪些包以及 org 文件存在于何处。

这个设置是一个正在进行的工作，可能比必要的更复杂。

## 修剪空格

我绝对讨厌额外的空白，文件末尾的换行符是必须的。以下是 Emacs 设置:

```
(add-hook 'before-save-hook 'delete-trailing-whitespace)
(setq require-final-newline t) 
```

Enter fullscreen mode Exit fullscreen mode

其中相当于这些 ST 选项:

```
"trim_trailing_white_space_on_save": true
"ensure_newline_at_eof_on_save": true 
```

Enter fullscreen mode Exit fullscreen mode

## 右括号插入

`autopair`包内置于 Emacs 中，将插入右括号。这不是最聪明的方法(比如在 org-mode 模式下在选中的单词上插入标记)，但它目前有效。此外，因为我假设它总是打开的，所以没有必要在我的模式行上显示它(我将在后面讨论)。此外，由于我现在使用 Emacs，并且我发现自己编辑越来越多的 lisp，我打开了一个模式，显示匹配的左括号或右括号(如果选择了一个)。

```
;; autopair
(autopair-global-mode)
(diminish 'autpair-mode)
(show-paren-mode 1) 
```

Enter fullscreen mode Exit fullscreen mode

## 大文件

st 最大的优点之一是它对大文件的处理。在 Emacs 中，我至少可以设置警告我大文件的阈值:

```
;; warn when opening files bigger than 100MB
(setq large-file-warning-threshold 100000000) 
```

Enter fullscreen mode Exit fullscreen mode

## 添加语言语法

Emacs 内置了对许多编码语言的支持，但是有时您需要确保为正确的文件名和/或扩展名触发正确的模式。这里有两个打开 CMake 文件支持的例子。

在第一个例子中，假设已经手动安装了`cmake-mode`:

```
;; CMake
(setq auto-mode-alist
      (append
       '(("CMakeLists\\.txt\\'" . cmake-mode))
       '(("\\.cmake\\'" . cmake-mode))
       auto-mode-alist)) 
```

Enter fullscreen mode Exit fullscreen mode

在第二个例子中，`use-package`语法用于延迟加载。这意味着我们不必记得打包安装 cmake-mode。这也意味着在我打开 cmake 文件之前，不会安装 CMake 模式包。

```
;; CMake
(use-package cmake-mode
  :mode ("\\.cmake\\'"
         "CMakeLists\\.txt\\'")
  :config (use-package cmake-font-lock)) 
```

Enter fullscreen mode Exit fullscreen mode

这里是另一个例子，但是对于铁锈:

```
;; rust
(use-package rust-mode
  :mode ("\\.rs\\'" . rust-mode)) 
```

Enter fullscreen mode Exit fullscreen mode

# 最精彩的来自崇高的文字

有太多的事情，崇高的文本只是得到正确的，有些事情我从来不知道我需要，直到他们在我的指尖。

## 项目视图

我所期待的 ST 的特性之一是项目工具条。拥有一个文件浏览器是我在项目中定位的关键。

为此，我使用 [Neotree](http://melpa.org/#/neotree) (一些文档在这里找到)。它的外观和感觉可以定制，但我还没有试验过。我坚持使用建议的键绑定< f8 >。要为 Mac 定制它，我必须将默认的系统命令改为`open`程序。

```
;; neotree
(use-package neotree
  :bind ([f8] . neotree-toggle)
  :config (setq neo-default-system-application "open")) 
```

Enter fullscreen mode Exit fullscreen mode

Neotree 在其缓冲区中支持一系列功能，包括“快速查找”和“在此添加文件”。唯一缺少的是相当于`dired-do-shell-command`的东西。这将允许我将光标放在一个文件上，并触发一些命令行输入。匹配(或类似)dired 的键绑定会很好，但至少 neotree 支持`?`键。

## 多个光标

可以说 st 最大的优点是多游标。一旦你知道了它，我在 ST 中打开了许多小东西来使用它。

Emacs 确实有自己的[多光标](http://melpa.org/#/multiple-cursors)包。不幸的是，在 Mac 上使用正确的
按键有几个问题。因此，我为此专门使用了< f2 >和< f3 >键。我还使用了 ESC ESC 作为 quit-this-mode 命令(类似于 ST 中的工作方式):

```
;; multiple-cursors
(use-package multiple-cursors
  :bind (("<f2>" . mc/mark-previous-like-this)
         ("<f3>" . mc/mark-next-like-this)
         ("C-c <f2>" . mc/mark-all-like-this)
         ("<ESC> <ESC>" . mc/keyboard-quit))
  :ensure t) 
```

Enter fullscreen mode Exit fullscreen mode

当你开始使用多游标模式时，它会问你几个问题，以确保你真的打算使用所有的游标。答案通常是肯定的。有时事情会变得混乱，多个光标在特定模式下会表现得很奇怪。如这里记载的，解决方法是删除`~/.emacs.d/.mc-lists.el`文件。

## 项目范围搜索

经过一些初步的探索，我仍然不能很好地进行跨文件搜索。内置的`M-x grep`做了一些好事，但是不太可靠。我将在以后的文章中进一步研究这个问题。

## 拼写检查

我的拼写很糟糕，所以拼写检查是必须的。Mac 上的 ST 可以打开系统范围的拼写检查器(具有记住/忽略单词的附加功能)。不幸的是，在 Emacs 上进行拼写检查要复杂得多。

Emacs 中的默认拼写程序是 ispell，即交互式拼写。要在 Mac 上安装 ispell 字典，你必须使用 homebrew 并告诉 Emacs 在哪里可以找到这个程序。

我在自定义文件中定义了 ispell 程序变量，因为位置是机器特定的。

```
(setq ispell-program-name "/usr/local/bin/aspell")
(setq ispell-list-command "--list") 
```

Enter fullscreen mode Exit fullscreen mode

唯一缺少的元素是一个永远在线的拼写检查器。另一个 Emacs 包， [Flyspell](https://www.emacswiki.org/emacs/FlySpell) ，就能做到这一点。现在我只需要默认启用文本类型缓冲区的`flyspell-mode`:

```
(dolist (hook '(text-mode-hook))
  (add-hook hook (lambda () (flyspell-mode 1))))
(dolist (hook '(change-log-mode-hook log-edit-mode-hook))
  (add-hook hook (lambda () (flyspell-mode -1)))) 
```

Enter fullscreen mode Exit fullscreen mode

# 在圣

现在有几件事我永远也不可能真正用 ST 完成，这包括深度 git 集成和打开 shell。当然 Emacs 支持这些东西！随着时间的推移，你会慢慢认为你永远都不应该离开 Emacs。

## 去吧

Emacs git 支持是通过 [magit](https://magit.vc) 完成的。这是一个了不起的项目，它让你不用离开 Emacs 就能完全控制 git。Magit 通过一系列弹出窗口工作，这意味着你只需要知道一个键绑定。一旦你进入 magit 弹出窗口，你就可以知道如何用`?`键做事情。

```
;; MAGIT
(use-package magit
  :bind ("C-x g" . magit-status)
  :ensure t) 
```

Enter fullscreen mode Exit fullscreen mode

## 外壳集成

不出所料，Emacs 提供了深度外壳集成。这篇关于[掌握 Emacs shell](https://masteringemacs.org/article/running-shells-in-emacs-overview)的帖子深入到了本质细节。可能需要一些设置来使`M-x shell`与 fish 一起正常工作。然而，`M-x ansi-term`似乎做了所有需要的事情，只是要小心不要在 Emacs 内部启动 Emacs。

对于非常简单的 shell 一行程序，总是有 dired 模式。命令`!`允许你在选择的文件上运行一个 shell 命令。这对于从 graphviz 文件生成点输出非常有用。我真的很想有一个从系统中打开所选文件的快捷方式，但是现在`! open`已经可以了。

# 组织模式

Emacs 最棒的地方是[组织模式](https://orgmode.org)。这里涉及的内容太多了，所以我将谈谈*为什么*你应该考虑 org-mode 并提供链接到 checkout。举个例子，这篇文章是用 org 模式创建的。与其他软件包不同，我在设置一台
新计算机时手动安装 org-mode。

## 基础语法&概述

Org-mode 很像 markdown，其核心是一种纯文本标记语法。下面是基本的标记:

| 影响 | 密码 |
| --- | --- |
| *斜体* | `/italic/` |
| **粗体** | `*bold*` |
| ~~击穿~~ | `+strike through+` |
| <u>下划线</u> | `_underlined_` |
| `code` | `~code~` |
| `verbatim` | `=verbatim=` |
| 

# Title 1

 | `* Heading 1` |
| 

## Title 2

 | `** Heading 2` |
| [链接文本](http://gonsie.com) | `C-c C-l`或`[[http://gonsie.com][Link Text]]` |

另外(如上所示)，org 支持电子表格(也就是真正智能的表格)。这种支持包括额外的空白，以便表格看起来总是对齐的。

Org-mode 对每个标题进行分段折叠。按 TAB 键将在子对象的可见性中循环。

Org-mode 还支持块，比如代码块、引号、例子等等:

```
#+BEGIN_SRC rust
fn sample_code () -> u32 {
    10
}
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

渲染到:

```
fn sample_code () -> u32 {
    10
} 
```

Enter fullscreen mode Exit fullscreen mode

```
#+BEGIN_QUOTE
Everybody lies
 -- Dr. House
#+END_QUOTE 
```

Enter fullscreen mode Exit fullscreen mode

渲染到:

> 每个人都会撒谎，豪斯医生

正如您将了解到的，在 org 文档中添加标记/选项/元数据的标准方式是在行首添加`#+SETTING`标记。这是 org 能够支持复杂的 latex 文档导出的方式。

## 识字编程

文化编程的理念是，一个单独的文档应该包括用来创建代码的思维过程以及代码本身。这适用于 R 脚本，在 R 脚本中，作者试图在展示和包含数据本身的同时讲述一个关于数据的故事。

### 导出

每个组织文档都可以导出或转换为不同语言的文档。导出选项包括 LaTeX、Beamer、Markdown、HTML 等。使用`C-c C-e`调出导出器窗格。

### 纠结

所有的碎片缠绕在一起。Tangling 获取一段写在文档中的代码并执行它。然后，它甚至可以将结果直接嵌入文档本身。

这里有两个 org 模式源代码块的例子。要生成结果块，可以在源块中键入`C-c C-c`。

#### 例 1: Shell 命令

```
#+BEGIN_SRC bash
hostname
#+END_SRC

#+RESULTS:
Eris.local 
```

Enter fullscreen mode Exit fullscreen mode

#### 例 2:生成并嵌入图像

```
#+BEGIN_SRC dot :file images/example_dot.png :cmdline -Kdot -Tpng
digraph g {
rankdir = LR;
a -> b;
}
#+END_SRC

#+RESULTS: 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/93c0d6f174c8dc030c86712743f0b2f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---50O0DeL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.gonsie.com/bloimg/example_dot.png)

我会让它渗透进去的。可能性确实是无穷无尽的。

## 议事日程

org-mode 的一个重要特性是它能够跟踪待办事项。设置和组织这个系统可能有点麻烦，所以我会避免说得太详细。相反，我鼓励你从最基本的设置开始，看看什么适合你。如果你决定组织模式的议程可以适合你的日常工作流程，我鼓励你看看其他人做了什么。

首先，您需要用一些键绑定来打开组织模式的议程功能。默认为:

```
;; org-mode agenda recommended keybindings
(global-set-key "\C-cl" 'org-store-link)
(global-set-key "\C-ca" 'org-agenda)
(global-set-key "\C-cc" 'org-capture)
(global-set-key "\C-cb" 'org-iswitchb) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要一种简单的方法来跟踪您创建的任何待办事项。我发现的最简单的方法是这个 on-save hook(来自[弗雷德里克·吉亚森](http://fgiasson.com/blog/index.php/2016/06/21/optimal-Emacs-settings-for-org-mode-for-literate-programming/))。通过这个钩子，每个 org 文件都被自动“归档”,里面的任何待办事项都会被 org 模式的议程生成器找到。

```
;; Add Org files to the agenda when we save them
(defun to-agenda-on-save-org-mode-file()
  (when (string= (message "%s" major-mode) "org-mode")
    (org-agenda-file-to-front)))

(add-hook 'after-save-hook 'to-agenda-on-save-org-mode-file) 
```

Enter fullscreen mode Exit fullscreen mode

这确实意味着当文件被删除或移动时，org agenda 会抱怨。

# 结论

我已经在 Emacs 的兔子洞里呆了两个月了。这很有趣，我很高兴继续修改我的设置和玩新的软件包。

在我短暂的探索中，我发现了一些非常有用的人:

*   Sacha Chua : Sacha 令人难以置信地鼓舞人心，并为 Emacs 社区做出了巨大贡献。她撰写 Emacs 和 Org news 的每周综述。
*   弗雷德里克·吉亚森:他有在保存时自动归档组织文件的技巧。
*   实用 Emacs 这个博客提供了许多在学术界和 LaTeX 中使用 Emacs 的技巧。
*   Aaron Bieber 有一篇关于使用 GUI Emacs 的博客文章，这说服了我去尝试。