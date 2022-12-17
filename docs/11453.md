# 用 grep 计算出现次数

> 原文：<https://dev.to/adamkdean/count-occurrences-with-grep-48f3>

今天我了解到 grep 有一个计算出现次数的“-c”开关。我再也不会用管道把它接到‘WC-l’了！

```
$ ps aux | grep -c adam
93 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们使用 wc，我们还需要对其进行修整:

```
$ ps aux | grep adam | wc -l
        94 
```

Enter fullscreen mode Exit fullscreen mode

所以，用-c。