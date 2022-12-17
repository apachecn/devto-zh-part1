# 在 Ubuntu 14.04 上安装 Consul

> 原文：<https://dev.to/adamkdean/install-consul-on-ubuntu-14-04-1eoa>

要在 Ubuntu 14.04 上安装 Consul，首先确保您有可用的解压缩程序:

```
$ apt-get install -y unzip 
```

Enter fullscreen mode Exit fullscreen mode

现在，获取领事档案，[确保获得最新的&正确的架构](https://dl.bintray.com/mitchellh/consul/)，在编写时是 0.5.0，对于 Ubuntu 是 linux_amd64:

```
$ wget https://dl.bintray.com/mitchellh/consul/0.5.0_linux_amd64.zip 
```

Enter fullscreen mode Exit fullscreen mode

现在拉开拉链。

```
$ unzip 0.5.0_linux_amd.zip 
```

Enter fullscreen mode Exit fullscreen mode

现在，将`consul`移到您道路上的某个地方:

```
$ mv consul /usr/bin/local/consul 
```

Enter fullscreen mode Exit fullscreen mode

最后，检查它的工作情况:

```
$ consul --version

Consul v0.5.0
Consul Protocol: 2 (Understands back to: 1) 
```

Enter fullscreen mode Exit fullscreen mode

干得好！