# 树莓派历险记开始了

> 原文：<https://dev.to/funkysi1701/the-raspberry-pi-adventure-starts-551d>

我现在正兴奋地谈论着我的树莓派。我将尝试在这里记录我迄今为止所做的事情。

我决定连同我的 Pi 一起购买相机模块。很容易将它们连接起来，提起网络端口后面的连接器，插入带状电缆，将连接器推回原位。

接下来，我连接了键盘、鼠标、显示器、SD 卡和电源。我的 Pi 有生命。

然后给了我一个满是安装选项的屏幕，Pidora 是一个基于 Fedora 的操作系统，我有一些经验，所以我用了那个。

安装完成后，我看到了一个安装了各种程序的标准桌面。

到目前为止，我还没有插上网线，因为这意味着要坐在大厅里路由器旁边，我想在我这样做之前，让我们看看 wifi 是否可用。我有一个 usb wifi 适配器，我用我的电脑，所以我把它插上。

什么都没发生，对 linux 有一些经验的我并不感到惊讶。我去“网络连接”填写了我的 wifi 连接的名称和密码，然后它就连接了。哇！那很容易。

于是我安装了 [webmin](http://www.webmin.com/download.html) ，这样我就可以远程管理它，并用 YUM 更新它。我还启动了 SSH 服务，这样我就可以在笔记本电脑上使用 putty 登录到我的 Pi。更好的是，我为我的智能手机找到了一个 SSH 客户端，这样我甚至可以从我的手机上控制我的 Pi！

编辑:我忘记了为了让 webmin 工作，我需要做以下事情。

mv/etc/red hat-release/etc/red hat-release . bak echo " Fedora release 20(Heisenbug)" >/etc/red hat-release

好了，现在我可以从浏览器配置我的 Pi 上的设置，或者使用 SSH(安全 SHell)登录来运行我喜欢的任何命令。

现在让我们查找如何使用相机。[文档](https://www.raspberrypi.org/documentation/configuration/camera.md)说摄像机首先需要使用命令 raspi-config 来启用。Pidora 中不存在此命令。让我们跳过它，看看它是否工作。确实如此。test.jpg 指挥官拍了一张我天花板的照片。

现在我如何远程查看这张图片。SSH 是一个只有命令行的界面，所以我可以看到已经创建了一个 2Mb 的文件。在我的 Pi 上安装一个 web 服务器怎么样？

yum 安装 httpd

这将安装 apache，它是默认的(也是非常流行的)web 服务器。

服务 httpd 启动

这将启动 web 服务器。转到 http://[IP]其中[IP]是您的 raspberry pi 的 IP 地址，您将看到默认的 apache 页面。

现在，如果你运行 raspi still-v-o/var/www/html/image1 . jpg，它将在网络服务器上创建你的照片。

对于我的第一步来说还不错，上面的命令可以被调度，每小时拍摄一次图像。还有录制视频的选项。