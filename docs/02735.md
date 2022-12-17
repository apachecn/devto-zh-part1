# 相似的东西，不一样

> 原文：<https://dev.to/saitoatsushi/similar-thing-not-same-cf9>

Common Lisp 有`do`语法。还有阴谋。

它们形式相同，但语义不同。

```
;; Common Lisp
(do ((i 0 (+ i 1))
     (j '() (cons (lambda()i) j)))
    ((= i 3)
     (mapcar (lambda(x)(funcall x)) j)))
;; → (3 3 3) 
```

Enter fullscreen mode Exit fullscreen mode

```
;; Scheme
(do ((i 0 (+ i 1))
     (j '() (cons (lambda()i) j)))
    ((= i 3)
     (map (lambda(x)(x)) j)))
;; → (2 1 0) 
```

Enter fullscreen mode Exit fullscreen mode

Scheme 的`do`每次为每个循环做一个新的位置。普通的 Lisp 不是这样的。