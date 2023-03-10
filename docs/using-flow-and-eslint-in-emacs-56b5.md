# 在 Emacs 中使用 flow 和 eslint

> 原文：<https://dev.to/atfzl/using-flow-and-eslint-in-emacs-56b5>

```
(require 'company)
(require 'web-mode)
(require 'flycheck)
(require 'flycheck-flow)

;; flow auto complete
;; skip this if you don't use company-mode
(eval-after-load 'company
  '(add-to-list 'company-backends 'company-flow))

;; add eslint and flow checkers to flycheck
(flycheck-add-mode 'javascript-eslint 'web-mode)
(flycheck-add-mode 'javascript-flow 'web-mode)

;;disable jshint since we prefer eslint checking
(setq-default flycheck-disabled-checkers
  (append flycheck-disabled-checkers
    '(javascript-jshint)))

(defun jsWithEslint ()
  "eslint for js files"
  (interactive)
  (web-mode)
  (web-mode-set-content-type "jsx")
  (flycheck-disable-checker 'javascript-flow)
  (flycheck-select-checker 'javascript-eslint)
  (flycheck-mode))

(defun jsWithEslintFlow ()
  "flow and eslint for js files"
  (interactive)
  (web-mode)
  (web-mode-set-content-type "jsx")
  (flycheck-select-checker 'javascript-eslint)
  (flycheck-add-next-checker 'javascript-eslint 'javascript-flow)
  (flycheck-mode))

;; set key shortcuts if you want
;; (global-set-key (kbd "C-c j") 'jsWithEslint)
;; (global-set-key (kbd "C-c f") 'jsWithEslintFlow)

(add-to-list 'auto-mode-alist '("\\.js\\'" . jsWithEslint))
(add-to-list 'magic-mode-alist '("/\\* @flow \\*/" . jsWithEslintFlow)) 
```

Enter fullscreen mode Exit fullscreen mode

现在所有的。js 文件将使用 eslint。

但是如果你在第一行有`/\* @flow \*/`注释，也会使用 flow。

要从 node_modules 中使用本地 eslint 和 flow-bin，请检查这个包: [add-node-modules-path](https://github.com/codesuki/add-node-modules-path) 。