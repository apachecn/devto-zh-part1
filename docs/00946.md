# 第 1 部分:使用 Org 开发 Elisp

> 原文：<https://dev.to/gonsie/part-1-using-org-to-develop-elisp-4ilc>

这篇文章是一系列文章的一部分。

*   (你在这里)第 1 部分:使用 Org 开发 E-lisp 代码片段
*   [第二部分:统计字数](///blorg/part2-counting-words.html)

* * *

*   有用的命令
*   自省的力量
*   从组织块开始

Org 模式的强大之处之一是能够运行代码块并收集输出。这允许您捕获代码、编写代码的上下文(作为实际文本)，并生成一个文档。这篇博文将描述我是如何使用 org 和 Emacs 本身开始学习 Emacs lisp 的。

## 有用的命令

在 Emacs 中，有许多方法可以触发相同的操作。我发现记忆和弦非常困难(如果我通过肌肉记忆而不是我的大脑知道我在键入什么来记忆的话)。这里有一个简短的命令表，我需要从 Emacs 内置的文档中获取。通常我通过`M-x FUNCTION`运行这些，如果我不记得确切的名字，这允许我完成制表。

| 弦 | 功能 | 结果 |
| --- | --- | --- |
| `C-h f FUNCTION` | `describe-function` | 显示函数的文档页 |
| `C-k F FUNCTION` | `Info-goto-emacs-command-node` | 显示函数的手册页 |
| `C-h k KEY` | `describe-key` | 显示和弦的文档页面 |
| `C-h K KEY` | `Info-goto-emacs-key-command-node` | 显示和弦的手册页 |
| `C-h C-f` | `view-emacs-FAQ` | 显示 GNU Emacs 常见问题 |
| `C-h i` | `info` | 将信息文档浏览器调出到您正在查看的最后一个信息页面 |
| `C-h i m elisp RET` |   | ELisp 文档的快捷方式 |
| `C-h i d` |   | 顶级信息目录 |
| `C-c C-c` | `org-ctrl-c-ctrl-c` | 伟大的函数名。做了一堆事情，也就是运行源代码块 |
| `C-c '` | `org-edit-special` | 在单独的缓冲弹出窗口中编辑块/表 |

有用的网页:

*   [Orgmode 源代码编辑](https://orgmode.org/org.html#Editing-source-code)
*   [组织简易模板](https://orgmode.org/org.html#Easy-templates)
*   [组织来源块题头参数](https://orgmode.org/org.html#Specific-header-arguments)
*   [Emacs](https://www.gnu.org/software/emacs/manual/html_node/eintr/index.html#Top)中 Lisp 上的 GNU 手册和[上的页面保存-偏移](https://www.gnu.org/software/emacs/manual/html_node/eintr/save_002dexcursion.html#save_002dexcursion)

## 自省的力量

我不通过教科书学习。通常，我发现他们有这些额外的叙述，而我真正想要的是等式或详细的函数文档。这确实让我学习新的编程语言有些困难，因为我不会坐下来看书。这就是 IDE 真正能帮上忙的地方。

> 在心理学中，内省的过程完全依赖于对一个人精神状态的观察。内省与人的自我反省密切相关，并与外部观察 <sup>[1](#fn.1)</sup> 相对照。

在编程中，自省是在程序运行时查看代码本身的能力。Emacs 通过`describe-function`和`describe-key`函数允许这样做(在某种意义上)。用户可以查看文档甚至源代码本身 <sup>[2](#fn.2)</sup> 。使用`describe-function`函数，以及制表符补全，您可以猜测可能存在的函数，并开始构建 e-lisp 代码片段。

内置文档在技术上不是自省，但它很好。要调出“Emacs Lisp”手册，请键入`C-h i m elisp RET`。

尽管我不喜欢叙述，但现在我将介绍一个基本的入门指南，介绍 Org 模式代码块。

## 从组织块开始

现在我们知道了如何在不离开 Emacs 的情况下获取文档，让我们来编码吧！我确信我们可以创建一个新的 elisp 文件/项目，但是谁有时间呢？当机会出现时，我宁愿做我的日常工作并学习一点新的 elisp。这意味着我已经在以 Org-mode 模式写作了。

### 基础知识:跑步和成绩

Org 源代码块包含可执行的代码片段。首先，确保通过 Org babel 执行 e-lisp 代码处于启用状态(通常在您的`init.el`或`.emacs`文件中):

```
(org-babel-do-load-languages
  'org-babel-load-languages
  '((emacs-lisp . t))) 
```

Enter fullscreen mode Exit fullscreen mode

让我们从一个非常基本的 Emacs lisp 片段开始。键入`<s TAB`会自动创建一个源代码块(这个叫做模板 <sup>[3](#fn.3)</sup> )。

```
#+NAME: calc
#+BEGIN_SRC elisp
(- 10 2)
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

点击`C-c C-c`时将光标放在块内会触发运行(键入`y`以允许代码片段在您的计算机上运行)。结果显示在文档中。

```
#+RESULTS: calc
        : 8 
```

Enter fullscreen mode Exit fullscreen mode

### 字符串操作

Elisp 不是我的首选计算器，但它是运行简单计算的便捷方式。让我们通过一些快速的文本解析变得更加复杂。在这里，我创建了 2 个项目列表，然后通过 Org 块打印所有的排列。

```
#+NAME: letters
- A
- B
- C

#+NAME: numbers
- 1
- 2

#+NAME: str-permute
#+BEGIN_SRC elisp :var s1=letters s2=numbers :results output
(while s1
  (setq x (car s1))
  (setq s1 (cdr s1))
  (setq tmp s2)
  (while tmp
    (setq y (car tmp))
    (setq tmp (cdr tmp))
    (princ (car x))
    (princ (car y))
    (princ "\n")))
#+END_SRC

#+RESULTS: str-permute
: A1
: A2
: B1
: B2
: C1
: C2 
```

Enter fullscreen mode Exit fullscreen mode

关于这个例子的一些注意事项:

1.  要编辑“Emacs-Lisp”模式缓冲区中的 e-lisp 代码，将光标移至源块并键入`C-c '`。这确保了适当的缩进和其他主要模式的准确性。注意，这可以用于任何主要模式 <sup>[4](#fn.4)</sup> ，包括文本。
2.  注意代码块 <sup>[5](#fn.5)</sup> 上的`:results output`头参数。有一堆论点，但结果总是让我困惑。在这种情况下，我希望打印代码片段执行时的输出，而不是代码片段的返回值。
3.  不要忘记`cdr`你的 while 循环变量。如果您忘记了，请使用`C-g`取消执行。
4.  Emacs 的打印功能很奇怪，文档也很迟钝…我只用`princ`。不幸的是，在一个单一的、可变参数的命令中很难`princ`多个条目(我也不想弄清楚如何构建字符串)。

## 脚注

<sup>[1](#fnr.1)</sup> [自省](https://en.wikipedia.org/wiki/Introspection)。*维基百科，免费百科*。2018 年 1 月 4 日访问。

<sup>[2](#fnr.2)</sup> 好吧，这在技术上不是程序化的自省，更像是“内置文档”另一方面，Emacs 在用户如何与文档和底层代码交互方面有些独特。

<sup>[3](#fnr.3)</sup> 用于[组织简易模板的文档](https://orgmode.org/org.html#Easy-templates)

<sup>[4](#fnr.4)</sup>[组织源块](https://orgmode.org/org.html#Editing-source-code)的文档

<sup>[5](#fnr.5)</sup> 用于[组织来源块标题参数的文档](https://orgmode.org/org.html#Specific-header-arguments)