# 哈苏拉地方发展

> 原文：<https://dev.to/sroy8091/hasura-local-development>

在我之前关于 web 应用 snipcode 的文章中，这是我实现这个目标的下一步。我必须设置一个本地开发服务器来测试和构建 web 应用程序，然后将其推送到 Github 并部署在 hasura 上。要设置本地开发服务器，以下是先决条件-

*   Virtualbox(虚拟方块)
*   最低 Ram - 4GB 和 64 位系统
*   Hasuractl
*   库贝特尔

Windows 用户的额外费用

*   git-bash 已安装

### 去巴士拉

现在，让我来完成所有这些产品的安装步骤。windows 中的首次 git-bash。

转到[链接](https://git-scm.com/download/win)并下载。64 位 git-bash 的. exe 文件并安装在您的 PC 上。对于 Linux 和 Mac 用户，这不是必需的。

### 虚拟盒

接下来是 VirtualBox。你要根据你的 os 从这个[链接](https://www.virtualbox.org/wiki/Downloads)下载 VirtualBox。

如果你用的是 Mac，你需要“OS X 主机”的版本。双击下载的文件进行安装。

如果您使用的是 Windows，则需要“Windows 主机”的版本。安装那个。请双击该文件。

如果你使用 Linux，你需要“Linux 发行版”,然后选择你的 PC 运行的发行版。下载文件后，您需要在终端上运行一些安装命令。对于 Debian 用户

> dpkg-I virtual box 文件. deb

对于 rpm 安装

> rpm -i virtualboxfile.rpm

### Hasuractl

安装所有这些之后，这一步是安装 hasuractl。对于 windows 用户，下载[hasuractl.exe](https://storage.googleapis.com/hasuractl/v0.1.2/windows-amd64/hasuractl.exe)并使用此[视频参考](https://drive.google.com/file/d/0B_G1GgYOqazYUDJFcVhmNHE1UnM/view)进行安装。

对于 linux 用户

> curl lo hasuractl[https://storage . Google APIs . com/hasuractl/v 0 . 1 . 2/Linux-amd64/hasuractl](https://storage.googleapis.com/hasuractl/v0.1.2/linux-amd64/hasuractl)&&【chmod+x hasuractl】&&sudo mv hasuractl/usr/local/bin/

它将下载 hasuractl 并将路径设置为您计算机中的 usr/local/bin。
同样适用于 Mac 用户，相反，该命令将为

> curl lo hasuractl[https://storage . Google APIs . com/hasuractl/v 0 . 1 . 2/Darwin-amd64/hasuractl](https://storage.googleapis.com/hasuractl/v0.1.2/darwin-amd64/hasuractl)&&【chmod+x hasuractl】&&sudo mv hasuractl/usr/local/bin/

### Kubectl

1.  对于 Mac 用户

*   使用 curl 下载最新版本

> curl-LO[https://storage . Google APIs . com/kubernetes-release/release/$(curl](https://storage.googleapis.com/kubernetes-release/release/%24(curl)-s[https://storage . Google APIs . com/kubernetes-release/release/stable . txt)/bin/Darwin/amd64/kubectl](https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl)

*   使 kubectl 二进制文件可执行

> chmod +x ./kubectl

*   将二进制文件移到您的路径中

> 须藤多美。/忽必烈/usr/local/bin/忽必烈

1.  对于 Linux 用户

*   使用 curl 下载最新版本

> curl-LO[https://storage . Google APIs . com/kubernetes-release/release/$(curl](https://storage.googleapis.com/kubernetes-release/release/%24(curl)-s[https://storage . Google APIs . com/kubernetes-release/release/stable . txt)/bin/Linux/amd64/kubectl](https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl)

*   使 kubectl 二进制文件可执行

> chmod +x ./kubectl

*   将二进制文件移到您的路径中

> 须藤多美。/忽必烈/usr/local/bin/忽必烈

1.  对于 Windows 用户

*   从[此链接](https://storage.googleapis.com/kubernetes-release/release/v1.6.3/bin/windows/amd64/kubectl.exe)下载最新版本 1.6.3。

*   将二进制文件添加到您的路径中。

### 首发 Hasura

安装完所有的先决条件后，现在是启动 hasura 本地开发的时候了。Windows 用户在 git-bash 中尝试这些命令。确保你在 hasura 有一个帐户，如果没有的话，请访问 [beta.hasura.io](https://beta.hasura.io) 并注册。

> hasuractl 登录

[![alt text](img/edcd3cdfbbf29b7d44d8577de0985979.png "login")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vFHicHlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3s67rrp5agtwkgcpsvg0.png)

-用于登录 hasura 帐户

> hasuractl 本地启动

[![alt text](img/edcd3cdfbbf29b7d44d8577de0985979.png "start")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vFHicHlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3s67rrp5agtwkgcpsvg0.png)

-用于启动本地开发和下载所有必要的工具。第一次需要时间，取决于互联网连接。

> hasuractl 本地站

[![alt text](img/18ad502d1ada490a46414495f29dab79.png "stop")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DcrYVX8W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iays9gcgbguigfmriop4.png)

-用于停止开发环境

如果出现任何错误，请通过清理本地设置重新启动该过程

> hasuractl 局部清洗

或者删除所有虚拟机和配置

> hasuractl 本地删除

*这里是关于这一系列 snipcode 开发的所有帖子的索引*

[第一部分:App Idea](https://dev.to/sroy8091/app-idea-for-hasura-internship)
[第二部分:App 原型](https://dev.to/sroy8091/app-prototype)
[第三部分:本地开发](https://dev.to/sroy8091/hasura-local-development)
[第四部分:G for Git](https://dev.to/sroy8091/g-for-git)
[第五部分:数据建模](https://dev.to/sroy8091/its-modeling-time)
[第六部分:Data&Auth API](https://dev.to/sroy8091/postman-a-major-tool-for-developers)
[第七部分:基本功能](https://dev.to/sroy8091/i-love-cookies-expressjscookies)
[第八部分:App 屏幕 1](https://dev.to/sroy8091/i-built-it-with-codemirror)
[第九部分](https://dev.to/sroy8091/app-screen-2)

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)T2】](https://ko-fi.com/A0A5WBC1) |