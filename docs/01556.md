# 当你开始用 Python 编程时，这些事情并不明显

> 原文：<https://dev.to/henbaku/things-that-werent-so-obvious-when-you-starting-to-program-in-python-2fe>

我用非常基础的语言在编程世界中介绍了自己。从 4 年前开始，我用 Python 创建了许多项目，但是尽管 Python Zen 说"*,应该有一种——最好只有一种——显而易见的方法来做这件事。*“有些事情并不明显。

## 单行条件句。

我的旧代码有太多多余的代码，特别是条件句:

```
if x < 56:
    some = "Dafaq"
else:
    some = "Nope" 
```

Enter fullscreen mode Exit fullscreen mode

没有比 put 更简单的了:

```
some = "Dafaq" if x < 56 else "Nope" 
```

Enter fullscreen mode Exit fullscreen mode

## 单行过滤列表。

Python 中最常见的情况之一是“过滤”列表。我的老办法:

```
a = [2, 24, 88, 32, 1, 6, 88, 10, 15, 34] # example list. b = []
for i in a:
    if i >= 15:
        b.append(i) 
```

Enter fullscreen mode Exit fullscreen mode

最佳方式:

```
a = [2, 24, 88, 32, 1, 6, 88, 10, 15, 34] # example list. b = [i for i in a if i >= 15] 
```

Enter fullscreen mode Exit fullscreen mode

## 一行求和列表值。

另一个最常见的事情是对列表中的值求和。我的老办法:

```
amounts = [2, 24, 88, 32, 1, 6, 88, 10, 15, 34] # example list. total = 0
for i in amounts:
    total += i 
```

Enter fullscreen mode Exit fullscreen mode

最佳方式:

```
amounts = [2, 24, 88, 32, 1, 6, 88, 10, 15, 34]
total = sum(amounts) 
```

Enter fullscreen mode Exit fullscreen mode

## 下一章...

当你开始使用 Django 时，事情并不那么明显...