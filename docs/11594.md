# Linux 下的 MAC 欺骗

> 原文：<https://dev.to/rndmh3ro/mac-spoofing-under-linux-22nk>

最近公布的利用 Firefox Javascript 漏洞攻击 Tor 用户的消息表明，在使用 Tor 时，有些事情需要保持匿名，比如禁用 Javascript、使用最新版本的 Firefox、安装防火墙等等。一个有趣的措施是经常改变你的 MAC 地址，这样就少了一个识别你和你的上网行为的向量。

我心想，为什么只和 tor 一起用，为什么不总是用？没有理由不去做。我们在这里寻找的术语叫做 [MACspoofing。有一篇简短但有趣的](https://en.wikipedia.org/wiki/MAC_spoofing)[论文](http://www.ijser.org/paper/Media_Access_Control_Spoofing_Techniques_and_its_Counter_Measures.html)讨论了这个话题。一定要看完。

在互联网上进行简短的搜索发现，已经有工具可以帮助你完成这项任务。我使用的应用程序被 Alvaro Lopez Ortega 称为“ [GNU MACChanger](https://github.com/alobbs/macchanger) ”。

### 安装

在 Arch Linux 上，macchanger 在 AUR，Ubuntu 的 Repos 里也有。所以只要用你最喜欢的软件包管理器安装它:

在 Arch Linux 上:

```
pakku -S macchanger 
```

关于 Ubuntu:

```
apt-get install macchanger 
```

### 用法

用法真的很简单，以 root 身份运行以下代码即可:

```
macchanger -r eth0 
```

这会将有线接口的 MAC 地址更改为随机值。如果你用的是 wifi，那就把 eth0 换成 wlan0 或者你的接口叫什么都行。

使用下面的命令，您可以将 MAC 地址更改为一个特定的值:

```
macchanger -m XX:XX:XX:XX:XX:XX 
```

其他选项可以在 macchanger 的联机帮助页中找到。

### 每次开机更换 MAC

如果想在每次引导时都运行转换器，可以在/etc/rc.d 中添加一个小函数，只需在这个目录中放入一个名为“change_mac”的文件，并插入 macchanger 命令。

```
echo "macchanger -r eth0" > /etc/rc.d/change_mac 
```

这样你每次启动都会得到一个新的 MAC 地址。