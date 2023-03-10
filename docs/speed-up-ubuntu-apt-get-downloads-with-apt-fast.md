# 用 apt-fast 加速 Ubuntu apt-get 下载

> 原文：<https://dev.to/dexterbrylle/speed-up-ubuntu-apt-get-downloads-with-apt-fast>

## [T1】简介](#intro)

几周前，我的 Ubuntu VirtualBox 下载速度很慢。最初，我以为我的互联网连接又出问题了(我家里有 8 Mbps 的连接)，所以我做了速度测试。然而，测试一致，这让我得出结论，这可能是一个 Ubuntu 镜像服务器的事情。半天过去了，连接仍然很困难，所以我清理了我的源列表，并从我的位置对我们使用的镜像中更改了它们:

```
 sudo sed -i 's/http:\/\/us./http:\/\//g' /etc/apt/sources.list 
```

## apt-fast

所以，速度又恢复正常了。然而，我一直在考虑从我设置的服务器下载它的延迟。通过几次谷歌搜索，我想出了这个伟大的软件包，它使用并行连接来加速下载。是[**——快**](https://github.com/ilikenwf/apt-fast) 。

> apt-fast 是一个用于 apt-get 和 aptitude 的 shellscript 包装器，它可以通过并行下载包，每个包有多个连接，从而大大缩短 apt 下载时间。

我感觉到了巨大的进步！你可以通过下面的命令来安装它:

```
sudo add-apt-repository ppa:saiarcot895/myppa
sudo apt-get update
sudo apt-get -y install apt-fast 
```

## 如何使用 apt-fast

你将使用 **apt-fast** 就像你使用 **apt-get** 或 **aptitude** 一样。

```
sudo apt-fast update
sudo apt-fast install -y <package-name>
sudo apt-fast dist-upgrade
sudo apt-fast remove 
```

## 进一步提高下载速度

您可以添加离您最近的镜子，以获得更好的效果。打开:

```
sudo vim etc/apt-fast.conf 
```

然后添加该行代码:

```
MIRRORS=( 'http://ftp.debian.org/debian, http://ftp2.de.debian.org/debian, http://ftp.de.debian.org/debian, ftp://ftp.uni-kl.de/debian' ) 
```

你可以在 Github [知识库](https://github.com/ilikenwf/apt-fast)上阅读更多关于 **apt-fast** 的信息。

*这篇文章最初发表在我的[博客](https://dexterbrylle.info/fast-apt-get-downloads-apt-fast/)T3 上*