# 在 OSX 上安装 Docker

> 原文：<https://dev.to/adamkdean/install-docker-on-osx-6jh>

要在 OSX 上安装 Docker，您需要安装一些先决条件。

1.  [安装自制软件](http://adamkdean.co.uk/blog/read/138/install-homebrew-on-osx)。
2.  [安装 VirtualBox](http://adamkdean.co.uk/blog/read/139/install-virtualbox-on-osx) 。
3.  [安装流浪汉](http://adamkdean.co.uk/blog/read/140/install-vagrant-on-osx)。

现在我们终于可以安装 Docker 了。我们还需要安装 boot2docker。

```
brew install docker
brew install boot2docker
boot2docker init
boot2docker up
export DOCKER_HOST=tcp://localhost:2375 
```

Enter fullscreen mode Exit fullscreen mode

并测试它:

```
docker version 
```

Enter fullscreen mode Exit fullscreen mode

请记住，如果 boot2docker 有一个不同于 2375 的端口，请使用正确的端口，也不要在尾部加上斜杠。