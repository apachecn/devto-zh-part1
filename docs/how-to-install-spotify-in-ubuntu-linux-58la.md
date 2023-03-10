# 如何在 Ubuntu Linux 中安装 Spotify

> 原文：<https://dev.to/xeroxism/how-to-install-spotify-in-ubuntu-linux-58la>

## 安装世界上最流行的流媒体应用程序之一。

[![spotify_welcome_screen](img/d4447f54b9ce9660186dce2bbf3785b0.png)T2】](https://i1.wp.com/fossnaija.com/wp-content/uploads/2017/12/spotify_welcome_screen.png?ssl=1)

*图像:化石人*

自 2008 年推出以来， [Spotify](https://www.spotify.com/us/) 已经成为不可忽视的主要音乐、播客和视频流媒体服务。与苹果音乐这样的巨头竞争。Spotify 是一项网络服务，提供来自唱片公司和媒体公司的受数字版权管理保护的内容。

Spotify 是免费增值服务；基本功能是免费的，没有广告或限制，而附加功能，如提高流媒体质量和音乐下载，则通过付费订阅提供。

可以按艺术家、专辑、流派、[播放列表](https://en.wikipedia.org/wiki/Playlist)或唱片公司搜索音乐。Spotify 与社交媒体高度集成，用户可以创建、编辑和共享播放列表，在[社交媒体](https://en.wikipedia.org/wiki/Social_media)上共享曲目，并与其他用户制作播放列表。你也可以浏览朋友、艺术家和名人的音乐收藏。

Spotify 不负众望，是一款出色的网络流媒体应用。它是跨平台的，这意味着它适用于大多数现代设备，包括 [Windows](https://en.wikipedia.org/wiki/Microsoft_Windows) 、 [macOS](https://en.wikipedia.org/wiki/MacOS) 和 [Linux](https://en.wikipedia.org/wiki/Linux) 电脑，以及 [iOS](https://en.wikipedia.org/wiki/IOS) 、 [Windows Phone](https://en.wikipedia.org/wiki/Windows_Phone) 和 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 智能手机和平板电脑。

在这篇文章中，我们将安装一个桌面版的 Linux 应用程序(debian 软件包)。也就是说，它是专门针对 debian 和基于 Debian 的 linux 操作系统的，比如 Ubuntu。

在 [Ubuntu](https://fossnaija.com/linux-installation-dual-booting-with-windows-7/) (或任何其他基于 debian 的 linux [发行版](https://fossnaija.com/10-common-terms-every-linux-user-should-know/))中打开[终端](https://fossnaija.com/10-common-terms-every-linux-user-should-know/)(Linux 的命令行界面)。**快捷键:Ctrl + Alt + T** 。

键入以下命令:

**# 1。添加 Spotify 存储库签名密钥，以便能够验证下载的包:**

 **`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0DF731E45CE24F27EEEB1450EFDC8610341D9410` 

**# 2。添加 Spotify 资源库**

 `echo deb http://repository.spotify.com stable non-free | sudo tee /etc/apt/sources.list.d/spotify.list`** 

 ****# 3。更新可用套餐列表**

 `sudo apt-get update` 

**# 4。安装 Spotify**

 `sudo apt-get install spotify-client` 

[![spotify_launch_ubuntu](img/cdc991087121af53032e1f90c4df71d1.png)T2】](https://i1.wp.com/fossnaija.com/wp-content/uploads/2017/12/spotify1-pic.png?ssl=1)

您现在可以享受 Spotify 了！！！

快乐的 Linux！

帖子[如何在 Ubuntu Linux 中安装 Spotify](https://fossnaija.com/install-spotify-ubuntu-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。**