# 从容器内获取容器 ID

> 原文：<https://dev.to/adamkdean/get-container-id-from-within-container-2keb>

要从容器中获取容器 ID，请使用以下命令:

```
$ cat /proc/self/cgroup | grep "docker" | sed s/\\//\\n/g | tail -1 
```

Enter fullscreen mode Exit fullscreen mode

我可能会在某个时候把它放在一个 npm 模块中。