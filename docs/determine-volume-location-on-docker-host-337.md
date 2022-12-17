# 确定 Docker 主机上的卷位置

> 原文：<https://dev.to/adamkdean/determine-volume-location-on-docker-host-337>

昨天，我遇到了以前遇到过的问题。它上次难倒了我，这次又难倒了我。我就是没*想*而且花了我太多时间。

我遇到的问题，我以前遇到过的问题，是我在一个容器中运行 Jenkins，通过 Docker 主机套接字，所以当 Jenkins 创建容器时，它们实际上在主机上运行。这很好，但是当您需要将文件从工作区直接映射到容器时，事情不会像您预期的那样进行。

这是因为主机正在运行 docker 命令，Jenkins 只是通过套接字与主机对话。因此，`/jenkins/jobs/yourjob/workspace/...`中的那些文件不会在主机上的那个位置。

我运行 Jenkins 的方式是，我有一个名为`jenkins_data`的数据量容器，正如您已经猜到的，它保存数据。为了使用`-v src:dest`卷图，我需要在主机上找到这些文件。码头工人检查在这里有帮助。

```
 docker inspect --format '{{ range .Mounts }}{{ if eq .Destination "/jenkins" }}{{ .Source }}{{ end }}{{ end }}' jenkins 
```

Enter fullscreen mode Exit fullscreen mode

一旦我有了位置，我需要对 Jenkins 公开的`$WORKSPACE`变量进行搜索和替换。之后，我知道了主机上的`/jenkins`卷的位置。

```
JENKINS_ROOT=$(docker inspect --format '{{ range .Mounts }}{{ if eq .Destination "/jenkins" }}{{ .Source }}{{ end }}{{ end }}' jenkins)
JENKINS_PATH=$(echo $WORKSPACE | sed "s|^\/jenkins|$JENKINS_ROOT|g") 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以从我的容器中直接映射文件或目录。

```
docker run d \
    -v $JENKINS_PATH/some/file.js:/var/lib/app/file.js \
    -v $JENKINS_PATH/dir:/var/lib/app/dir \
    someapp 
```

Enter fullscreen mode Exit fullscreen mode