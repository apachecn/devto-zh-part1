# 如何在 Macbook Pro 上设置 Manjaro Linux i3

> 原文：<https://dev.to/lobo_tuerto/how-to-setup-manjaro-linux-i3-on-a-macbook-pro-p1e>

* * *

你可以在 [lobotuerto 的笔记——如何在 Macbook Pro 上设置 Manjaro Linux i3 查看这篇文章的最新更新版本。](https://lobotuerto.com/blog/how-to-setup-manjaro-linux-i3-on-a-macbook-pro/)

* * *

我已经注意到了一个新的发行版，它与 **i3** 一起发布: [Manjaro Linux i3 社区版。](https://manjaro.org/category/community-editions/i3/)

它有一些粗糙的边缘，但我很肯定，考虑到曼加罗的人气上升，这些问题迟早会得到解决。

我能说的是，Manjaro Linux 感觉敏捷，同时 Ubuntu 开始感觉臃肿，行动迟缓。

因此，接下来是一个非常固执己见的指南，从 2014 年年中开始在 MacBook Pro 上安装 Manjaro Linux，并让它处于桌面和软件开发使用的完美状态——就像我目前在 Ubuntu 上使用的那样。

仍然在寻找一个完美的操作系统。开始吧！

# 下载

*   直接下载和校验和:[这里。](https://sourceforge.net/projects/manjarolinux-community/files/i3/)
*   Torrent 下载:[这里。](https://sourceforge.net/projects/manjarotorrents/files/community/i3/)

# 准备活动 USB

你需要另一台 Linux 机器，或者想办法做一个 Manjaro Linux live 盘。

一旦你下载了**。iso** 镜像你可以按照[这些说明](https://lobotuerto.com/blog/how-to-create-an-ubuntu-live-usb-using-the-cli/)来准备 u 盘。

# 从 USB 引导

关闭机器，插入 USB 驱动器，然后打开，当听到启动时的独特声音时，按住`alt`键，直到出现引导菜单。选择写着**电喷**的那个。

您将看到一个预配置屏幕。选择您的时区、键盘分布、语言、免费驱动程序，然后: **Boot: manjaro.x86…**

# 安装

你会看到一个有很多按钮的对话框，点击其中一个按钮:**启动安装程序**。

您可能会看到几条*不可读的*警告，它们通常是关于:

*   机器没有连接电源。
*   机器没有连接到互联网。

当你进入**分区**时，选择**擦除磁盘**清除 Mac 上的所有东西(不要担心，如果你真的想的话，你总是可以恢复 OSX 的——但不是你的数据，所以要小心！).

此时，点击**加密系统**复选框，为其选择一个漂亮的长密码，然后点击安装向导，等待安装过程完成。

# 配置

## 高 DPI 屏幕

首先要做的是提高我们在终端上阅读那种超级小文本的能力。

使用
检查您的屏幕支持的模式(分辨率)

```
xrandr 
```

Enter fullscreen mode Exit fullscreen mode

如果最初的 DPI 对你来说太难了——对我来说也是——尝试几个分辨率，看看你觉得哪种分辨率合适。就我而言，我通常会选择`1680x1050`或`1400x900`。

像这样尝试一下:

```
xrandr --output eDP1 --mode 1680x1050 
```

Enter fullscreen mode Exit fullscreen mode

为了使其永久化，编辑`~/.i3/config`并在末尾添加这一行:

```
exec --no-startup-id xrandr --output eDP1 --mode 1680x1050 
```

Enter fullscreen mode Exit fullscreen mode

## 键盘

第二个恼人的问题是键盘，因为我喜欢在我的桌面环境中快速移动，换出来的键没有帮助，让我们来解决这个问题。

让我们交换一下 **Alt** 和 **Cmd** 键。

即刻生效:

```
echo 1 | sudo tee /sys/module/hid_apple/parameters/swap_opt_cmd 
```

Enter fullscreen mode Exit fullscreen mode

使其永久化:

```
echo options hid_apple swap_opt_cmd=1 | \
sudo tee -a /etc/modprobe.d/hid_apple.conf 
```

Enter fullscreen mode Exit fullscreen mode

让我们默认启用功能键(f1、f2、F3……)，而不是默认的多媒体键。

即刻生效:

```
echo 2 | sudo tee /sys/module/hid_apple/parameters/fnmode 
```

Enter fullscreen mode Exit fullscreen mode

使其永久化:

```
echo options hid_apple fnmode=2 | \
sudo tee -a /etc/modprobe.d/hid_apple.conf 
```

Enter fullscreen mode Exit fullscreen mode

## 蛴螬

我通常在`/etc/default/grub`中从 grub 中移除`quiet`选项。我喜欢我的创业过程*嘈杂。*

如果你编辑了`grub`文件，不要忘记:

```
sudo update-grub 
```

Enter fullscreen mode Exit fullscreen mode

## 固态硬盘

如果您有固态硬盘，您可以使用
激活默认每周运行的定期整理操作

```
sudo systemctl enable --now fstrim.timer 
```

Enter fullscreen mode Exit fullscreen mode

它基于的时间戳是:`/var/lib/systemd/timers/stamp-fstrim.timer`。

## UFW

启动时使用
激活防火墙服务

```
sudo ufw enable
sudo systemctl enable --now ufw.service 
```

Enter fullscreen mode Exit fullscreen mode

## SSH

如果您想允许传入的 SSH 连接通过防火墙，请尝试以下方法:

```
sudo ufw allow SSH 
```

Enter fullscreen mode Exit fullscreen mode

修改并取消注释`/etc/ssh/sshd_config` :
中的以下行

```
PermitRootLogin no
PasswordAuthentication no 
```

Enter fullscreen mode Exit fullscreen mode

启用 SSH 服务:

```
sudo systemctl enable --now sshd.service 
```

Enter fullscreen mode Exit fullscreen mode

如果您曾经修改过`/etc/ssh/sshd_config`文件，请使用:
重新启动 SSH 服务

```
sudo systemctl restart sshd.service 
```

Enter fullscreen mode Exit fullscreen mode

## WiFi

我首先通过 USB-以太网适配器电缆连接解决了这个问题— [这个](https://www.amazon.com.mx/dp/B00MYTSN18/ref=cm_sw_r_tw_dp_U_x_JiqvAb1YE37YZ)到目前为止工作得很好。

首先，让我们生成一个已排序的镜像列表，然后更新包数据库:

```
sudo pacman-mirrors --geoip
sudo pacman -Syy 
```

Enter fullscreen mode Exit fullscreen mode

如果你想调整它，可以在这里找到镜像列表:`/etc/pacman.d/mirrorlist`。

升级您已安装的软件包:

```
sudo pacman -Su 
```

Enter fullscreen mode Exit fullscreen mode

用`uname -a`找出你安装了哪个内核版本。

然后让我们安装合适的头文件和驱动:

```
sudo pacman -S linux-headers broadcom-wl-dkms 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，我选择了`linux414-headers`。

让我们重新开始尝试目前为止的配置！

你现在应该可以用 **WiFi** 了。

如果您没有这样做，并且在您的`journalctl -b`日志中看到类似这样的内容:

```
kernel: wl: version magic '4.14.15-1-MANJARO SMP preempt mod_unload modversions retpoline ' should be '4.14.15-1-MANJARO SMP preempt mod_unload modversions ' 
```

Enter fullscreen mode Exit fullscreen mode

你也许可以用这个来强制激活 WiFi:

```
sudo modprobe wl --force-vermagic 
```

Enter fullscreen mode Exit fullscreen mode

它在这里工作得很好。

## 声

这是一个惊喜，声音不是开箱即用。好消息是，它只需要一些调整，我们会很好。

编辑此文件:

```
sudo nano /etc/modprobe.d/alsa-base.conf 
```

Enter fullscreen mode Exit fullscreen mode

并放入:

```
options snd_hda_intel enable=1 index=0
options snd_hda_intel enable=1 index=1 
```

Enter fullscreen mode Exit fullscreen mode

安装`pulseaudio`和`pavucontrol` :

```
sudo pacman -S pulseaudio pavucontrol 
```

Enter fullscreen mode Exit fullscreen mode

此外，静音键可以静音，但再次按下它不会解除声卡的静音，要解决这个问题，请在您的`~/.i3/config` :
后面加上这个

```
bindsym XF86AudioMute exec --no-startup-id amixer -D pulse sset Master toggle 
```

Enter fullscreen mode Exit fullscreen mode

这个需要重启系统。

重新启动后，你应该有声音从你的电脑里发出来，还有一组有效的多媒体键。

# 日常使用情况

## 复制&裱糊

你可以用三个手指在触控板上触摸/点击来粘贴内容。

# 软件

## Utils

```
sudo pacman -S calibre namcap pacaur redshift 
```

Enter fullscreen mode Exit fullscreen mode

要在 init 上执行`redshift`,编辑`~/.i3/config`,并在末尾添加这一行:

```
exec --no-startup-id redshift 
```

Enter fullscreen mode Exit fullscreen mode

不知道[红移是什么？](http://jonls.dk/redshift/)

## 文件管理器

如果你想念文件管理器的舒适空间，你可以用 [Thunar](https://en.wikipedia.org/wiki/Thunar) :
得到一个不错的空间

```
sudo pacman -S thunar 
```

Enter fullscreen mode Exit fullscreen mode

## 浏览器

这个发行版附带了苍白的月亮浏览器。我试过了，但不喜欢。

摆脱它:

```
sudo pacman -Rs palemoon-bin
rm -rf ~/.moonchild\ productions/ 
```

Enter fullscreen mode Exit fullscreen mode

如果你想安装另外两个——火狐和谷歌 Chrome:

```
sudo pacman -S firefox
pacaur -S google-chrome 
```

Enter fullscreen mode Exit fullscreen mode

## 代码编辑器

```
pacaur -S visual-studio-code-bin 
```

Enter fullscreen mode Exit fullscreen mode

## 网络服务发现

如果你有网络打印机，这很有用:

```
sudo systemctl enable --now avahi-daemon.service 
```

Enter fullscreen mode Exit fullscreen mode

现在 CUPS 应该可以毫无问题地检测到它。

转到这个网址`http://localhost:631`，在那里添加你的网络打印机！

# 日常工作流程准备好了吗？

绝对**是的！**

# 链接

*   [archi wiki–macbook pro 11，x](https://wiki.archlinux.org/index.php/MacBookPro11,x)
*   [如何让 Alsa 自动挑选喜欢的声音设备？](https://superuser.com/questions/626606/how-to-make-alsa-pick-a-preferred-sound-device-automatically)
*   [Visual Studio 代码](https://wiki.archlinux.org/index.php/Visual_Studio_Code)