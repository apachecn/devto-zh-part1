# 在两个容器之间共享目录

> 原文：<https://dev.to/adamkdean/share-directories-between-two-containers-1md0>

*最初发布于 2015 年 6 月 1 日* ( [更多信息](https://dev.to/adamkdean/migrating-to-dev-to-190)

*注:这是在 2015 年 docker 和现代集装箱化的初期发布的。如今，我们能够简单地使用卷。关于现代方式的更多信息，请看 [docker volumes](https://docs.docker.com/storage/volumes/) 。*

创建数据卷容器:

```
docker run -d \
    -v /var/test/ \
    --name test-data \
    busybox 
```

Enter fullscreen mode Exit fullscreen mode

使用它启动一个容器:

```
docker run -d \
    --name test-1 \
    --volumes-from test-data \
    adamkdean/baseimage bash -c "while true; do echo 'ping'; sleep 5; done" 
```

Enter fullscreen mode Exit fullscreen mode

使用它开始另一个:

```
docker run -d \
    --name test-2 \
    --volumes-from test-data \
    adamkdean/baseimage bash -c "while true; do echo 'ping'; sleep 5; done" 
```

Enter fullscreen mode Exit fullscreen mode

附加到第一个:

```
docker exec -ti test-1 bash
root@7bfff33a2309:/# cd /var/test/
root@7bfff33a2309:/var/test# ls 
```

Enter fullscreen mode Exit fullscreen mode

附加到第二个:

```
docker exec -ti test-2 bash
root@7bfff33a2309:/# cd /var/test/
root@69e9a3cc34a2:/var/test# ls 
```

Enter fullscreen mode Exit fullscreen mode

在第一个文件中创建一个文件:

```
root@7bfff33a2309:/var/test# touch test-file 
```

Enter fullscreen mode Exit fullscreen mode

检查第二个:

```
root@69e9a3cc34a2:/var/test# ls
test-file 
```

Enter fullscreen mode Exit fullscreen mode

`test-1`和`test-2`都在共享数据卷容器`test-data`上的数据。