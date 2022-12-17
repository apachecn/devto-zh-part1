# 编译时编程的嘶嘶声

> 原文：<https://dev.to/saitoatsushi/fizz-buzz-with-compile-time-programming-754>

Fizz Buzz 是计算机程序员的筛选问题。它有时也用于深奥的编程游戏。

Kazuho [用 C++写了一个程序](http://blog.kazuhooku.com/2017/11/fizzbuzz.html)在编译时产生一串嘶嘶的嗡嗡声。gos-k 也[用 Common Lisp 写了同样概念的](http://gos-k.hatenablog.com/entry/2017/11/13/205634)。

而我是用 Scheme (R6RS)写的。

```
(import (rnrs))

(define-syntax make-fizzbuzz-string
  (lambda(ctx)
    (syntax-case ctx ()
      ((_ n)
       (integer? (syntax->datum #'n))
       (call-with-string-output-port
         (lambda(port)
           (do ((i 1 (+ i 1)))
               ((< (syntax->datum #'n) i))
             (display
              (cond
               ((zero? (mod i 15)) "fizzbuzz")
               ((zero? (mod i 5))  "buzz")
               ((zero? (mod i 3))  "fizz")
               (else i))
              port)
             (newline port))))))))

(define (fizzbuzz)
  (display (make-fizzbuzz-string 100)))

(fizzbuzz) 
```

Enter fullscreen mode Exit fullscreen mode

对于编译时编程，LISP 比 C++容易得多。