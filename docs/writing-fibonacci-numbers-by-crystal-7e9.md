# 用水晶书写斐波那契数

> 原文：<https://dev.to/msky026/writing-fibonacci-numbers-by-crystal-7e9>

fyi [使用 Ruby 到 Go 的函数→快速](http://qiita.com/grj_achm/items/679b3f3af2cf377f0f02)。

我试着用水晶写斐波那契数列，代码是同样的红宝石。
这不同于只有 finename 扩展。

```
def fib(n)
  return n if n <= 1
  fib(n - 1) + fib(n - 2)
end

puts fib(40) 
```

Enter fullscreen mode Exit fullscreen mode

结果如下。

(红宝石:2.2.2，水晶:0.9.1)

```
 $ time ruby fib.rb
102334155

real  0m17.136s
user  0m17.050s
sys 0m0.063s 
```

Enter fullscreen mode Exit fullscreen mode

```
$ time crystal fib.cr
102334155

real  0m1.960s
user  0m0.990s
sys 0m0.400s 
```

Enter fullscreen mode Exit fullscreen mode

```
$ crystal build fib.cr --release
$ time ./fib
102334155

real  0m0.646s
user  0m0.639s
sys 0m0.004s 
```

Enter fullscreen mode Exit fullscreen mode

go(1.5.1)

```
$ time go run fib.go
102334155

real    0m1.386s
user    0m1.216s
sys 0m0.116s 
```

Enter fullscreen mode Exit fullscreen mode

```
$ time ./fib
102334155

real    0m0.765s
user    0m0.755s
sys 0m0.006s 
```

Enter fullscreen mode Exit fullscreen mode

相比之下没有太大的区别。

## 点

[增加创建动态库的能力](https://github.com/manastech/crystal/issues/921)