# 第 2 部分:计算单词

> 原文：<https://dev.to/gonsie/part-2-counting-words-2dng>

这篇文章是一系列文章的一部分。

*   [第 1 部分:使用 Org 开发 E-lisp 代码片段](///blorg/part1-org-for-dev-elisp.html)
*   (你在这里)第二部分:数单词

* * *

*   探索现有功能
*   标记和计数
*   贾恩廷
*   数数所有的东西！

我经常使用一个单独的(Org)文件来开发书面内容，尤其是当我填写一份有论文问题的申请时。答案通常有字数或字符数的限制。让我们用 Emacs 和 Org 来统计字数吧！

## 探索现有功能

首先，我使用内置文档(和制表符补全)来编译一些有用的函数。事实上，我挥舞了很多，并窃听了我的朋友谁知道一堆关于 Emacs 和 Org。

`count-words`函数显然是一个很好的起点。文档指出函数既可以作用于区域，也可以作用于缓冲区，如果从 lisp 调用(而不是交互调用)，它的行为会有所不同。我对区域一无所知，但是 Emacs Lisp 文档中的“区域”提到了两个更有用的函数:`region-beginning`和`region-end`。

纵观所有的 Org 函数，有两个非常突出:`org-mark-subtree`和`org-babel-mark-block`。这些函数在指定的 Org 内容块上创建一个区域。

最后，我需要前往 Org 位置来标记区域。有几个函数看起来不错:`org-goto`(尽管这看起来非常具有交互性)和`org-id-goto`。还有，我碰到了`org-babel-goto-named-src-block`。我已经知道如何命名源块。对于其他 go-to，创建一个 Org ID 非常简单:`org-id-get-create`。(注意，尽管`org-id-new`看起来很有前途，但我最终发现它并没有做我想要的事情)。

## 标记和计数

我需要为我想字数统计的每个答案创建一个组织部分。我可以使用一个头/节点/子树或者一个源块(文本)。在第一种情况下，我将在头中添加一个惟一的 ID(通过调用`org-id-get-create`)，这样我就可以使用`org-id-goto`。在源代码块的情况下，我可以简单地给它一个名字。

```
** Describe Blah (100 words)
:PROPERTIES:
:ID: 460D55A4-7C61-485C-8778-075406BAE8A4
:END:

My answer is that blah.

#+NAME: question1
#+BEGIN_SRC text
Blah blah blah.
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

计算子树中单词的代码片段类似于:

```
#+NAME: subtree-method
#+BEGIN_SRC elisp
(org-id-goto "460D55A4-7C61-485C-8778-075406BAE8A4")
(org-mark-subtree)
(count-words (region-beginning) (region-end))
;; note that this snippet does not work
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，第一个片段不起作用。相反，它显示错误“错误的类型参数:markerp nil”。

让我们试试源块方式:

```
#+NAME: src-block-method
#+BEGIN_SRC elisp
(org-babel-goto-named-src-block "question1")
(org-babel-mark-block)
(count-words (region-beginning) (region-end))
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

万岁。这个有用。奇怪的是，结果出现在问题块下面，而不是我期望的地方(代码片段下面)。

### 巡庭

我的朋友(他实际上是读书的)向我展示了一个很棒的 Emacs lisp 函数 <sup>[6](#fn.6)</sup> :

> `save-excursion`:保存点，当前缓冲区；执行主体；还原那些东西。

结果，这个函数遍布于一堆 e-lisp 代码中。我们试试吧！一、计数单词的子树版本:

```
#+NAME: subtree-method
#+BEGIN_SRC elisp
(save-excursion
  (org-id-goto "460D55A4-7C61-485C-8778-075406BAE8A4")
  (org-mark-subtree)
  (count-words (region-beginning) (region-end)))
#+END_SRC 
```

Enter fullscreen mode Exit fullscreen mode

低看哪！突然起作用了。另外，结果出现在当前代码块的下面。使用`save-excursion`也修复了源块方法，使结果块出现在它附近。

子树方法的一个遗留问题是，它将 Org 属性抽屉计入字数。

## 清点所有的东西！

最后，我们的字数统计片段应该在一堆子树或块上工作。让我们向代码片段传递一些输入。此外，我们应该打印结果。最后，我还加入了计算字符数的代码。源代码块方法最终看起来像:

```
#+NAME: questions
- question1
- question2
- question3

#+BEGIN_SRC elisp :var blks=questions :results output
(save-excursion
  (while blks
    (setq b (car (car blks)))
    (setq blks (cdr blks))
    (org-babel-goto-named-src-block b)
    (org-babel-mark-block)
    (princ b)
    (princ "\t")
    (princ (count-words (region-beginning) (region-end)))
    (princ " words\t")
    (princ (- (region-end) (region-beginning)))
    (princ " chars\n")))
#+END_SRC

#+RESULTS:
: question1 3 words 16 chars
: question2 3 words 16 chars
: question3 3 words 16 chars 
```

Enter fullscreen mode Exit fullscreen mode

对于子树方法也可以这样做，但是使用 id 作为输入。

## 脚注

<sup>[6](#fnr.6)</sup> 用于 [Emacs Lisp 保存-偏移](https://www.gnu.org/software/emacs/manual/html_node/eintr/save_002dexcursion.html#save_002dexcursion)的文档