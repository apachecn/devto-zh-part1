# Arch Linux 上的 humble bundle download agent(hi b-dl agent)

> 原文：<https://dev.to/rndmh3ro/humblebundle-downloadagent-hib-dlagent-on-arch-linux-3am8>

我从那些卑微的家伙那里购买了几乎所有卑微的独立包。我也不想从网站手动下载和安装游戏，因为毕竟我用的是 Linux 和一个不错的包管理器， *pacman* 。幸运的是，有了 [AUR](https://aur.archlinux.org/) 和 *hib-dlagent* ，我可以通过 pacman 安装和更新游戏。 [![humble-bundle-logo](img/eb24c047c3d88b53d7d4d246cc1d0c6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F3LRosjc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zufallsheld.img/humble-arch-logo.png)

hib-dlagent 是一个“通过文件名下载简单的独立软件包二进制文件的工具”。它与包构建实用程序 *makepkg* “一个自动化包构建的脚本”集成在一起。Makepkg 用于从 AUR 下载和安装软件包。

要安装 hib-dlagent，请在您喜欢的 shell 中输入以下内容:

```
pakku -S hib-dlagent-git 
```

然后你必须编辑 make pgk . conf(*/etc/make pkg . conf*)，这样当从 AUR 安装游戏时，就会使用 *hib-dlagent* 。将以下内容添加到“DLAGENTS”部分:

```
'hib::/usr/bin/hib-dlagent -u $YOUREMAIL -p $YOURPASSWORD -o %o %u' 
```

插入您登录 HumbleBundle 网站时使用的电子邮件地址和密码。最后，该部分应该是这样的:

```
#-- The download utilities that makepkg should use to acquire sources
# Format: 'protocol::agent'
DLAGENTS=('ftp::/usr/bin/curl -fC - --ftp-pasv --retry 3 --retry-delay 3 -o %o %u'
      'http::/usr/bin/curl -fLC - --retry 3 --retry-delay 3 -o %o %u'
      'https::/usr/bin/curl -fLC - --retry 3 --retry-delay 3 -o %o %u'
      'rsync::/usr/bin/rsync --no-motd -z %u %o'
      'scp::/usr/bin/scp -C %u %o'
      'hib::/usr/bin/hib-dlagent -u $YOUREMAIL -p $YOURPASSWORD -o %o $(echo %u | cut -c 7-)') 
```

当你完成后，你可以像往常一样从 AUR 安装游戏，例如，要安装非常好的 [FTL](http://www.ftlgame.com/) ，使用:

```
pakku -S ftl 
```