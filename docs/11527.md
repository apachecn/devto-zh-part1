# 在 Linux 中计算目录中的文件数

> 原文：<https://dev.to/adamkdean/count-files-in-a-directory-in-linux-22e4>

计算当前目录中的文件数:

```
ls | wc -l 
```

Enter fullscreen mode Exit fullscreen mode

或者使用以下方法计算特定目录中的文件数:

```
ls /some/path | wc -l 
```

Enter fullscreen mode Exit fullscreen mode