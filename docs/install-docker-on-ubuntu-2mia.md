# 在 Ubuntu 上安装坞站

> 原文：<https://dev.to/adamkdean/install-docker-on-ubuntu-2mia>

要在 Ubuntu 14.04 LTS 版上安装 Docker，请运行以下命令:

```
$ sudo apt-get install docker.io
$ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker 
```

Enter fullscreen mode Exit fullscreen mode

完成后，将您的用户添加到 docker 组:

```
$ sudo usermod -a -G docker adam 
```

Enter fullscreen mode Exit fullscreen mode

此后，您可能需要注销并再次登录。我遇到了一个问题，我收到了这样一个错误:

```
$ docker ps
dial unix /var/run/docker.sock permission denied 
```

Enter fullscreen mode Exit fullscreen mode

一旦你退出并再次登录，你可以测试 docker 是否已经安装并正常工作；

```
$ docker version
Client version: 0.9.1
Go version (client): go1.2.1
Git commit (client): 3600720
Server version: 0.9.1
Git commit (server): 3600720
Go version (server): go1.2.1
Last stable version: 0.11.1 
```

Enter fullscreen mode Exit fullscreen mode

请务必在[开始](https://www.docker.io/gettingstarted/)阅读更多内容