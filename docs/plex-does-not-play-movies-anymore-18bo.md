# Plex 不再播放电影

> 原文：<https://dev.to/rndmh3ro/plex-does-not-play-movies-anymore-18bo>

我使用 [Plex](https://www.plex.tv/) 来管理和观看我个人家庭存储系统中的所有电影、电视节目和音乐。它由两个程序组成:

*   在我的服务器上运行的 Plex 媒体服务器
*   我的 Android 电视盒子上运行的 Android 应用程序，连接到客厅的电视。

这通常非常有效。但是有一天晚上我和女朋友想看电影，它突然不想开始了。相反，这个非常有用的错误信息出现在电视屏幕上:

[![plex_error](img/1a3c5cc89f430eb41c27877d008ce1ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QWCjdCi5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zufallsheld.img/plex_error.png)

这对我们来说是一个新的开始。我知道，如果我现在不解决这个问题，这个晚上就会被毁了！

所以要解决这个问题。第一个想法:重启一切。但这并没有帮助，电影仍然没有播放。我开始思考问题可能是什么。我的第一个想法是，电影不知何故损坏或有一些音频或视频编解码器不支持。但我很快就打消了这个想法，因为我知道这部电影在我第一次测试时播放得很好。所以我试着运行一些别的东西，并注意到，这些节目似乎很有效！

几分钟已经过去了，在让这个晚上浪费掉并可能激怒我的女朋友之前，我决定和她一起看仍然在工作的电视节目。

快进到第二天，电影仍然没有播放。电视节目很成功。尝试其他电影，我注意到根本没有电影在播放！所以我开始在网上寻找线索。人们说的第一件事是确保我有最新版本的 Plex 媒体服务器和 Android 应用程序。他们是，我不记得我最近更新了软件。

在进一步搜索后，我在 Reddit 上找到了这个[主题](https://www.reddit.com/r/PleX/comments/4b8thz/plex_wont_play_movies_but_works_with_tv_shows/):

> 我在运行 OS X 10.11.3 的 2011 Macbook Pro 上安装了 Plex，电视节目播放正常，大约为 1.5 GBs，但我尝试的两部电影根本无法播放。这些文件是大约 15-20 GBs 的 h264 MKVs

这似乎表明我的临时文件空间不够大:

> 这是因为您没有足够的临时空间来转码整个文件。这是一张在几天前更新的支票。在设置中，您可以将 temp transcode 文件夹指向一个有更多空间的驱动器(我将我的指向了我的 NAS 上的一个新共享)，一切都会恢复正常。

该设置可在以下位置找到:

[![plex](img/4d13d32e0a2572ce350f1ee9f3219b00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9X01QYG7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zufallsheld.img/plex.png)

这听起来和我遇到的问题一模一样！但是检查服务器显示有足够的临时空间可用！此时，我已经花了一个多小时查找错误，并考虑放弃重新安装 Plex 的服务器端。但这将意味着重新索引我的整个媒体收藏，这可能会持续几个小时。

我决定推迟这个，回到客厅，看看重新安装 Android 应用程序(再次)是否可行，或者在此期间是否发生了任何神奇的事情，可以让电影再次工作。

当然，重新安装没有帮助，它本身也不工作。在浏览了 Android 应用程序中的各种选项后，我看到了以下设置:

[![plex_android_settings](img/49fe7055a922c590e43f64cca8b5224a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eT8qjjA9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zufallsheld.img/plex_android_settings.png)

然后我突然想起来我曾经去掉了这两个复选标记，现在它们又出现了！我取消了这两个选项，我所有的电影又可以播放了。我去了 App Store，查看了 Plex 应用程序最后一次更新的时间。确实是几天前了。似乎此更新重置了应用程序设置。

因此，如果你发现自己处于同样的情况，请检查以下内容:

*   检查软件最近是否更新过，并查看使用旧版本是否可以解决问题
*   检查更新的版本是否解决了问题
*   如果 Plex 媒体服务器上的代码转换器目录中有足够的空间
*   检查 Android 应用程序中的音频设置，并取消选择杜比数字和/或杜比数字加

关系挽回了！