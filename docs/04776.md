# 为什么我总是推荐 Arch Linux

> 原文：<https://dev.to/jvanbruegge/why-i-always-recommend-arch-linux>

通常，新手的首选是 Ubuntu。它带有一个很好的安装程序和一个“足够好”的界面。但是我总是向新手推荐 Antergos Linux，因为它是一个基于 Arch Linux 的发行版。为什么？

## 软件

在 Ubuntu 中，软件通常比当前版本落后整整一年。因为维护人员希望软件经过测试，而不是像新版本那样经常出错。然而，以我的经验来看，情况恰恰相反:我刚刚经历了另一个“事件”。目前我不得不使用 ubuntu，因为它不是我的电脑。我使用 Keepass 并试图通过我的 WebDAV 服务器同步密码数据库。

它不起作用。为什么？因为 Ubuntu 安装的 Mono 版本(“最新”版本)是当前稳定版本之后的一个完整的主要版本，而这个旧版本不能处理 letsencrypt 证书。搜索一天后的解决方案:添加官方 mono ppa 作为存储库，并使用那里的版本。

有了 Arch(和基于 Arch 的发行版),你总能在主软件库中获得最新版本**。我从未在我的 Arch 机器上遇到过这个问题，原因很简单，因为安装 keepass 时也下载了最新版本的 mono。**

## 官方资料库中没有的软件

在 Ubuntu 上，如果你的软件不在官方的软件仓库里，你大部分时间都不走运。如果你幸运的话，软件维护者正在托管一个 ppa，你可以从那里安装它。如果没有，就要下载一个`.deb`手动更新。

另一方面，Arch Linux 有 AUR——Arch 用户库。每个用户都可以上传一个包构建脚本来打包任何可用的软件。结果是，如果一个软件适用于 linux，你可以在 AUR 找到它。这也意味着您可以获得这些用户软件包的正常软件包更新。

## 包管理器

Ubuntu 的包管理器非常冗长，所以你必须输入很多才能完成普通的任务。更新所有已安装的软件包:

```
sudo apt update && sudo apt upgrade 
```

Enter fullscreen mode Exit fullscreen mode

相比之下，pacman 是一个重要的 Linux 软件包管理员:

```
sudo pacman -Syu 
```

Enter fullscreen mode Exit fullscreen mode

如果您想通过包管理器访问 AUR，最常见的方法是在`pacman`周围安装`yaourt`包装器。有了这个你可以很容易地搜索软件包的数据库:

```
yaourt vlc 
```

Enter fullscreen mode Exit fullscreen mode

## TL；速度三角形定位法(dead reckoning)

如果你是 Linux 初学者，安装 Antergos Linux。一旦您熟悉了命令行，就切换到 bare Arch Linux。