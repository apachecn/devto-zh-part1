# 移除所有码头集装箱

> 原文：<https://dev.to/adamkdean/remove-all-docker-containers-53j2>

在使用 Docker 仅仅几个小时后，我现在拥有的旧容器比我的手指还多。

删除这些内容的一个快速简单的方法是运行:

```
$ docker rm $(docker ps -q -a); 
```

Enter fullscreen mode Exit fullscreen mode

`-q`是安静模式，只返回容器 id。

`-a`是 all，顾名思义，返回所有容器。

这也会尝试删除任何正在运行的容器，但是会失败。