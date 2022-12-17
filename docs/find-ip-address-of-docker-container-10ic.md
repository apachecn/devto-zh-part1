# 查找码头工人集装箱的 IP 地址

> 原文：<https://dev.to/adamkdean/find-ip-address-of-docker-container-10ic>

要获得 Docker 容器的 IP 地址，可以使用下面的命令:

```
$ docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${CID} 
```

Enter fullscreen mode Exit fullscreen mode

假设容器的 ID 是`a2150s`，我们可以这样得到 IP:

```
$ docker inspect --format '{{ .NetworkSettings.IPAddress }}' a2150s

172.17.0.2 
```

Enter fullscreen mode Exit fullscreen mode

我为此写了一个脚本，`/usr/local/bin/dockerip` :

```
#!/bin/bash

COUNT=`docker ps | grep $1 | wc -l | bc`

if [ $COUNT -gt 0 ]; then
  IP_ADDRESS=`docker inspect --format '{{ .NetworkSettings.IPAddress }}' $1`
  echo "$1: $IP_ADDRESS"
else
  echo "Could not find $1\. Check container is running."
fi 
```

Enter fullscreen mode Exit fullscreen mode

这将构成有用的 docker 脚本库的一部分。