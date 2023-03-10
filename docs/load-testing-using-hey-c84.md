# 使用 Hey 进行负载测试

> 原文：<https://dev.to/saantoryuu/load-testing-using-hey-c84>

我试图对我使用 Apache AB 构建的一个服务进行基准测试，并遇到了一些问题(该死的 AB 不支持 HTTP/1.1)。我查了 AB 的替代品，发现[嘿](https://github.com/rakyll/hey)。它是用 Golang 写的，非常漂亮。

在这篇文章中，我将带你在你的实例和一个简单的基准上完成 golang 和 hey 的设置。Hey 需要 1.7 及以上的 Go 版本，这使得设置过程看起来很耗时，因为 yum repo 安装 Go 1.6，任何安装 Go 1.7 的 Google 搜索都会遇到一个`wget, tar, export and make`集合。幸运的是，有 [gvm](https://github.com/moovweb/gvm) ，这为我们简化了事情。

## 安装 Go1.7 和嘿

1.  运行`bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)`
    这将安装 GVM。

2.  安装 Go1.4 并将其设为默认。这是需要的，因为 Go 1.5 及以上版本的某些部分是自举的，所以 Go 1.7 需要一个现有版本的 Go 来自行安装。(如果安装步骤失败，尝试使用`--binary`，如这里的[所示](https://github.com/moovweb/gvm/issues/287)。
    `gvm install go1.4`
    `gvm use go1.4`

3.  运行`gvm install go1.7`

4.  切换到 hey `gvm use go1.7`要求的版本

5.  检查是否使用`echo $GOPATH`配置了 GOPATH

6.  安装嘿。`go get -u github.com/rakyll/hey to install hey`

7.  确认安装`which hey`

好了。我们安装了嘿。

## 简单的基准

`hey -n 10 -c 2 -m POST -T "application/x-www-form-urlencoded" -d 'username=1&message=hello' http://your-rest-url/resource`

这会打开 2 个连接，并发送 10 个请求。每个请求都是一个表单 post，带有两个到您的资源的参数。根据需要调整命令以添加/删除更多标志。