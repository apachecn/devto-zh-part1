# 与 Emacs 和 Go 一起开发

> 原文：<https://dev.to/yorodm/desarrollando-con-emacs-y-go-45cg>

# 与 Emacs 和 Go 一起开发。

我有一个非常健康的习惯，就是尽我所能使用 Emacs(甚至检查邮件)，所以当我决定在 [Go](https://golang.org) 开始一些项目时，我很高兴没有语言的 IDE，而且如果有很多工具、实用程序和插件让每个人都把房子给自己

## 制作 IDE 分为三个步骤。

将 **Emacs** 转换为 **Go** 的开发环境是一项相当简单的任务。我们把它分成三个步骤。

### 安装必要的工具。

安装了 **Go** 和 **Git** 后，我们需要一套工具(官方和第三方)使我们的生活简单得多。

1.  [错误检查](http://github.com/kisielk/errcheck):检查编译错误。
2.  [Guru](http://golang.com/x/tools/cmd/guru) :获取有关代码的信息。
3.  [Gocode](http://github.com/nsf/gocode) :完成
4.  [Gorename](http://golang.org/x/tools/cmd/gorename) :在一定程度上进行了重构。
5.  [汇入](http://golang.org/x/tools/cmd/goimports):协助新增或移除汇入的套件(也格式化程式码)。

这些工具本身对我们没有多大帮助，因此任何支持扩展的文本编辑器都可以轻松集成这些工具。

### 埃尔拉多·德·埃马克

在我的套餐清单上(我只用稳定的 melpa】，在 **Go** 中约有 15 个与开发有关的套餐。就个人而言，我不需要太多的工作语言。我添加的功能包括:

1.  语言并用 **go-mode** 和 **company-go** 完成。
2.  使用 **go-errcheck** 检查错误。
3.  与 **go-eldoc** 交互帮助。
4.  与 **go-rename** 重构。
5.  使用 **go-guru** 进行代码检查。
6.  整合力量**炮弹**维亚**围棋炮弹**

除了随附于工具来源 **gocode** 中的【T2 公司-go】之外，建议您通过 **package-install** 安装所有套件，并从该处收费，以避免版本冲突。

### 帕索决赛。

以所有的成分来说，我们只剩下 2 分钟的时间在**【go】**做为样品，只剩下:

1.  将`$GOPATH/bin`加入`$PATH`
2.  修改**init .**以自订 **Emacs** 的套件。

第一个是取决于你所在的操作系统，但无论如何，这是一项微不足道的任务。如果你在 **Emacs** 中相对年轻，你很可能不知道怎么做第二件事，没关系，下面是我的设置(没有键盘快捷键，这已经是非常个人化的事情了)。

```
;; Si estás utilizando correctamente tu gestor de paquetes
;; no tienes que adicionar las lineas de los 'require'
;; Adicionar el backend de Go a company
(require 'company)
(add-to-list 'company-backends 'company-go)
;; Activamos go-mode
(require 'go-mode)
;; y toda la familia
(require 'go-guru)
(require 'go-errcheck)
(require 'go-projectile)
;; Añadimos un hook para que cuando se active el modo
;; se configuren todas estas cosas
(add-hook 'go-mode-hook
    (lambda ()
        (company-mode)
        (projectile-mode)
        (go-eldoc-setup)
        (add-hook 'before-save-hook 'gofmt-before-save)
        (setq gofmt-command "goimports"))) 
```

Enter fullscreen mode Exit fullscreen mode

...就这样。我们创建了一个**ide**来在 **Go** 上工作，而不离开我们最喜欢的编辑的舒适性。