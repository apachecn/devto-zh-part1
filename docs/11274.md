# 移除所有退出的容器

> 原文：<https://dev.to/adamkdean/remove-all-exited-containers-3gli>

这将删除所有退出的容器。

```
docker ps -a | grep 'Exited' | awk '{print $1}' | xargs --no-run-if-empty docker rm 
```

Enter fullscreen mode Exit fullscreen mode

请小心使用，因为它还会移除数据卷容器。